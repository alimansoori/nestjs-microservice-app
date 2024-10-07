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

## Project Setup

### Cloning the Repository
To clone the project along with all the submodules:
```bash
git clone --recursive git@github.com:alimansoori/nestjs-microservice-app.git
