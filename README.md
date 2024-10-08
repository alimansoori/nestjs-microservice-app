
# NestJS Microservice App

## Overview
This project is a microservices architecture built with [Nest.js](https://nestjs.com/). It consists of independent services, such as user management, payments, and a frontend, all coordinated by an API Gateway.

### Submodules:
- **[API Gateway](https://github.com/alimansoori/api-gateway)**: Routes requests to the appropriate services.
- **[Payments Service](https://github.com/alimansoori/payments-service)**: Handles payment-related operations.
- **[Users Service](https://github.com/alimansoori/users-service)**: Manages user data and authentication.
- **[Front Service](https://github.com/alimansoori/front-service)**: Handles frontend interactions.

### Key Technologies
- **Nest.js**: Backend framework for building the microservices.
- **Bull**: Queue management system for task scheduling.
- **Redis**: Used for caching and managing queues.
- **TypeORM / Mongoose**: For database connections, depending on the service.
- **Docker & Docker Compose**: For containerizing and orchestrating the services.
- **NATS**: Message broker for inter-service communication.

## Live Demos
- **Project Demo**: [https://alimansoori.com](https://alimansoori.com)
- **API Gateway Demo**: [https://api.alimansoori.com](https://api.alimansoori.com)
- **API Gateway Documentation**: [https://api-gateway-doc.alimansoori.com](https://api-gateway-doc.alimansoori.com)
- **Users Service Documentation**: [https://users-service-doc.alimansoori.com](https://users-service-doc.alimansoori.com)
- **Payments Service Documentation**: [https://payments-service-doc.alimansoori.com](https://payments-service-doc.alimansoori.com)

## Project Setup

### Cloning the Repository
To clone the project along with all the submodules:
```bash
git clone --recursive git@github.com:alimansoori/nestjs-microservice-app.git
```

If you've already cloned the repository, initialize the submodules using:
```bash
git submodule update --init --recursive
```

### Running the Project

#### Development Environment
To run the project in development mode with Docker Compose:
```bash
npm run start:dev
```

To bring down the development environment:
```bash
npm run start:dev:down
```

#### Production Environment
To run the project in production mode:
```bash
npm run start:prod
```

To bring down the production environment:
```bash
npm run start:prod:down
```

### Docker Compose Files
The project includes separate Docker Compose configurations for development and production environments:

- **docker-compose.dev.yml**: Development environment setup with live reloading for services, using local directories for source code.
- **docker-compose.prod.yml**: Production environment setup optimized for performance.

### Network and Volumes
The services communicate via a shared Docker network called `microservice-network`, and each service that requires a MySQL database has its own persistent volume to store data.

#### Custom Networks
To ensure services are isolated yet can communicate, Docker Compose uses the `microservice-network` which must be created before starting the services:
```bash
docker network create microservice-network
```

### Environment Variables
Each service requires environment variables, such as database credentials and API keys. You can find example `.env` files in the root directory of each service. Copy and adjust these as needed:

```bash
cp .env.example .env
```

### Documentation
The project generates documentation for each service using [Compodoc](https://compodoc.app/). Documentation is served on different ports:
- API Gateway: [http://localhost:4000](http://localhost:4000)
- Users Service: [http://localhost:4001](http://localhost:4001)
- Payments Service: [http://localhost:4002](http://localhost:4002)

You can access these URLs after running the services.

## Contributing
Contributions are welcome! Feel free to open issues or submit pull requests. Please ensure you follow the coding standards outlined in the [CONTRIBUTING.md](CONTRIBUTING.md) file.

## License
This project is licensed under the [MIT License](LICENSE).
