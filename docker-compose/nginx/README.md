
# Setting Up Nginx with Let's Encrypt SSL Certificates Using Docker

This guide will help you set up **Nginx** with **Let's Encrypt** SSL certificates using **Docker** and **Docker Compose**. Follow these step-by-step instructions to configure your environment so that other developers can easily onboard and run the project.

---

## Prerequisites

- **Docker** and **Docker Compose** installed on your system.
- A domain name and subdomain names pointing to your server's IP address.
- Ports **80** and **443** open on your server's firewall.

---

## Project Structure

```
project/docker-compose/nginx
├── docker-compose.yml
├── default.conf
└── letsencrypt/  # This directory will store Certbot logs
```

---

## Step 1: Set Up the `docker-compose.yml` File

Create a `docker-compose.yml` file with the following content:

```yaml
version: '3.4'

services:
  nginx:
    image: nginx:stable-alpine
    container_name: nginx
    restart: always
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./default.conf:/etc/nginx/conf.d/default.conf
      - certs:/etc/letsencrypt
      - certbot-www:/var/www/certbot
    networks:
      - microservice-network

  certbot:
    image: certbot/certbot
    container_name: certbot
    volumes:
      - certs:/etc/letsencrypt
      - certbot-www:/var/www/certbot
    networks:
      - microservice-network
    command: [
      "certonly", 
      "--webroot", 
      "--webroot-path=/var/www/certbot", 
      "--email", "your-email@example.com", 
      "--agree-tos", 
      "--no-eff-email", 
      "-d", "domain.com", 
      "-d", "api.domain.com", 
      "-d", "api-gateway-doc.domain.com", 
      "-d", "users-service-doc.domain.com", 
      "-d", "payments-service-doc.domain.com"
    ]

volumes:
  certs:
  certbot-www:

networks:
  microservice-network:
    driver: bridge
```

**Notes:**

- The `nginx` service uses the official `nginx:stable-alpine` image.
- The `default.conf` file is mounted directly from the host machine.
- The `certbot` service is configured to obtain SSL certificates.
- Replace `your-email@example.com` and `domain.com` with your actual email and domain name.

---

## Step 2: Create the Nginx Configuration File

Create a `default.conf` file at `~/work/nginx/default.conf` with the following content:

```nginx
server {
    listen 80;
    server_name domain.com api.domain.com api-gateway-doc.domain.com users-service-doc.domain.com payments-service-doc.domain.com;

    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }

    location / {
        return 200 'Temporary Nginx Server';
        add_header Content-Type text/plain;
    }
}
```

**Notes:**

- This configuration allows Nginx to serve the necessary files for Let's Encrypt domain verification.
- Replace `domain.com` with your actual domain name.

---

## Step 3: Create Necessary Directories

Ensure the directories for mounting volumes exist on your host machine:

```bash
mkdir -p ~/work/nginx/letsencrypt
```

---

## Step 4: Create the Docker Network

If the `microservice-network` does not exist, create it using:

```bash
docker network create microservice-network
```

---

## Step 5: Start Nginx Without SSL Configuration

We will first run Nginx without SSL to allow Let's Encrypt to verify the domain ownership.

1. **Start Nginx:**

   ```bash
   docker-compose up -d nginx
   ```

2. **Verify Nginx Is Running:**

   ```bash
   docker ps
   docker logs nginx
   ```

3. **Test Nginx:**

   Visit `http://domain.com` in your browser. You should see a page displaying "Temporary Nginx Server".

---

## Step 6: Obtain SSL Certificates Using Certbot

Run Certbot to obtain SSL certificates from Let's Encrypt:

```bash
docker-compose run certbot
```

**Notes:**

- Certbot will use the `webroot` method to verify domain ownership.
- If successful, certificates will be stored in the `certs` volume.

---

## Step 7: Update Nginx Configuration with SSL

Now that we have obtained the SSL certificates, update the `default.conf` file to configure SSL:

```nginx
server {
    listen 80;
    server_name domain.com;
    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }
    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    listen 443 ssl;
    server_name domain.com;

    ssl_certificate /etc/letsencrypt/live/domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/domain.com/privkey.pem;

    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_pass http://api-gateway:3000;
        proxy_http_version 1.1;
    }
}
```

**Notes:**

- The server now listens on port 443 with SSL enabled.
- HTTP requests are redirected to HTTPS.
- Replace `your-backend-service` and `port` with your backend service's hostname and port.
- Ensure that the SSL certificate paths match the ones generated by Certbot.

---

## Step 8: Reload Nginx with SSL Configuration

1. **Restart Nginx:**

   ```bash
   docker-compose restart nginx
   ```

2. **Verify Nginx Is Running Without Errors:**

   ```bash
   docker logs nginx
   ```

---

## Step 9: Test HTTPS Access

Visit `https://domain.com` in your browser to verify:

- The SSL certificate is valid.
- The site loads correctly.
- HTTP requests are properly redirected to HTTPS.

---

## Step 10: Set Up Automatic Certificate Renewal

Let's Encrypt certificates expire every 90 days. Set up a cron job to automatically renew the certificates:

1. **Open Crontab Editor:**

   ```bash
   crontab -e
   ```

2. **Add the Following Line:**

   ```cron
   0 0 * * * docker-compose run certbot renew --quiet && docker-compose exec nginx nginx -s reload
   ```

**Notes:**

- This cron job runs daily at midnight to check for certificate renewal.
- If certificates are renewed, Nginx is reloaded to apply the new certificates.

---

## Additional Notes

- **Firewall Configuration:**

  Ensure that ports 80 and 443 are open in your server's firewall settings.

- **DNS Settings:**

  Your domain name must point to your server's public IP address.

- **File Permissions:**

  Ensure that the user running Docker has permission to access the mounted directories and files.

- **Backend Service:**

  Make sure that `your-backend-service` is reachable from the Nginx container, possibly by adding it to the same Docker network.

---

## Troubleshooting

- **Nginx Not Starting:**

  Check the logs for errors:

  ```bash
  docker logs nginx
  ```

- **Certbot Errors:**

  View Certbot logs for more details:

  ```bash
  cat ~/work/nginx/letsencrypt/letsencrypt.log
  ```

- **Cannot Access Site via HTTPS:**

  Ensure that the SSL certificates are correctly installed and that Nginx is configured to use them.

---

## Useful Commands

- **Bring Up Services:**

  ```bash
  docker-compose up -d
  ```

- **Stop Services:**

  ```bash
  docker-compose down
  ```

- **View Running Containers:**

  ```bash
  docker ps
  ```

- **Execute Command in Running Container:**

  ```bash
  docker exec -it nginx sh
  ```

---

## References

- [Nginx Official Documentation](https://nginx.org/en/docs/)
- [Let's Encrypt Documentation](https://letsencrypt.org/getting-started/)
- [Certbot Documentation](https://certbot.eff.org/docs/)
- [Docker Documentation](https://docs.docker.com/)

---

## Conclusion

By following this guide, you have successfully set up Nginx with SSL certificates from Let's Encrypt using Docker. This configuration ensures secure HTTPS communication for your domain and allows for easy maintenance and scalability.

---

If you have any questions or need further assistance, feel free to contact us at [your-email@example.com](mailto:your-email@example.com).
