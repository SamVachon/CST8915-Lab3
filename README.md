# CST8915 Lab 3 — Deploying the Algonquin Pet Store on Azure

**Student Name:** Samuel Vachon  
**Student ID:** 041101891  
**Course:** CST8915 Full-stack Cloud-native Development  

## Demo Video

[Watch the Lab 3 Demo](https://youtu.be/58D-7WZ3f8Q)

## Service Repositories

- [Order Service](https://github.com/SamVachon/CST8915-Lab2-Order-Service)
- [Product Service](https://github.com/SamVachon/CST8915-Lab2-Product-Service)
- [Store Front](https://github.com/SamVachon/CST8915-Lab2-Store-Front)

## Deployment Overview

| Component | Deployment |
|-----------|------------|
| RabbitMQ | Azure Ubuntu VM with Docker |
| Order Service | Node.js on Azure App Service |
| Product Service | Rewritten in Python with Flask on Azure App Service |
| Store Front | Vue.js on Azure Static Web Apps |

The deployed storefront loads products from the product service and submits orders to the order service. The order service sends the orders to RabbitMQ's `order_queue`. Testing confirmed that placing an order through the storefront added a message to the queue.

## Reflection Questions

### 1. What challenges did you encounter when configuring environment variables in the GitHub Actions workflow?

I needed to place the environment variables under the correct job in the storefront's GitHub Actions workflow and use the exact deployed backend URLs. I learned that Vue uses these variables during the build, so changing them requires rebuilding and redeploying the storefront. After adding `VUE_APP_ORDER_SERVICE_URL` and `VUE_APP_PRODUCT_SERVICE_URL` and committing the workflow, the storefront successfully connected to both services.

### 2. How does deploying microservices on Azure Web App Service differ from running them locally?

Locally, I install dependencies and start each service from a terminal. On Azure App Service, Azure manages the hosting infrastructure, while I configure the runtime, deployment workflow, startup command, and environment variables. The services also use public HTTPS URLs instead of localhost. I had to configure the RabbitMQ VM's inbound network rule so the deployed order service could reach it.

### 3. Why is it important to use environment variables for configurations in a cloud environment?

Environment variables let me change settings such as service URLs, ports, and RabbitMQ connections without modifying the application code. This makes it easier to use the same code locally and on Azure. Backend secrets can be stored in Azure's application settings instead of being committed to GitHub. Frontend variables should contain only public configuration, because their values are included in the built JavaScript.

## Setup Challenges

- GitHub Actions deployment setup initially failed with an OpenID Connect authentication error. I enabled SCM basic authentication and selected basic authentication in Deployment Center, which allowed continuous deployment to be configured.
- Orders initially timed out because the RabbitMQ port rule had been created as an outbound rule. Creating an inbound TCP rule for port 5672, restricted to the order service's outbound IP addresses, resolved the connection issue.
