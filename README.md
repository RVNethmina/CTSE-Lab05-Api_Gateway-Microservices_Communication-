# ECommerce Platform — API Gateway & Microservices

A containerised e-commerce backend built with **Next.js**, **MongoDB Atlas**, and **NGINX**, demonstrating an **API Gateway + Microservices** architecture pattern.

> **SE4010 — Current Trends in Software Engineering | Lab 05**

---

## Architecture Overview

```
Client
  │
  ▼
┌─────────────────────────────┐
│      API Gateway (NGINX)     │  :8080
│  /items   → item-service     │
│  /orders  → order-service    │
│  /payment → payment-service  │
└─────────────────────────────┘
        │           │           │
        ▼           ▼           ▼
  item-service  order-service  payment-service
     :8081         :8082          :8083
  (Next.js)     (Next.js)      (Next.js)
        │           │               │
        ▼           ▼               ▼
   MongoDB      MongoDB          MongoDB
  (item-db)   (order-db)      (payment-db)
```

All services communicate over a shared Docker bridge network (`microservices-net`). Clients interact only with the API Gateway — direct access to individual services is not required.

---

## Services

| Service | Internal Port | Exposed Port | Responsibility |
|---|---|---|---|
| `api-gateway` | 8080 | **8080** | NGINX reverse proxy — routes requests to the correct microservice |
| `item-service` | 3000 | 8081 | Manages product/item catalogue |
| `order-service` | 3000 | 8082 | Manages customer orders |
| `payment-service` | 3000 | 8083 | Manages payment records |

---

## Tech Stack

- **Runtime**: [Next.js](https://nextjs.org/) (App Router, API Routes) + TypeScript
- **Database**: [MongoDB Atlas](https://www.mongodb.com/atlas) via Mongoose
- **API Gateway**: [NGINX](https://nginx.org/)
- **Containerisation**: Docker & Docker Compose

---

## Project Structure

```
ECommerce_Platform/
├── docker-compose.yml          # Orchestrates all services
├── api-gateway/
│   ├── Dockerfile
│   └── nginx.conf              # Route definitions
├── item-service/               # Next.js app — items CRUD
│   ├── app/items/route.ts
│   ├── model/Item.ts
│   └── lib/mongodb.ts
├── order-service/              # Next.js app — orders CRUD
│   ├── app/orders/route.ts
│   ├── model/Order.ts
│   └── lib/mongodb.ts
└── payment-service/            # Next.js app — payments CRUD
    ├── app/payments/route.ts
    ├── model/Payment.ts
    └── lib/mongodb.ts
```

---

## API Reference

All requests should be sent to the **API Gateway** on port `8080`.

### Items — `/items`

| Method | Endpoint | Description | Request Body |
|---|---|---|---|
| `GET` | `/items` | List all items | — |
| `POST` | `/items` | Create a new item | `{ "name": "Headphones" }` |
| `GET` | `/items/:id` | Get a single item | — |

### Orders — `/orders`

| Method | Endpoint | Description | Request Body |
|---|---|---|---|
| `GET` | `/orders` | List all orders | — |
| `POST` | `/orders` | Create a new order | `{ "itemId": "...", "quantity": 2, "customerId": "..." }` |
| `GET` | `/orders/:id` | Get a single order | — |

### Payments — `/payment`

| Method | Endpoint | Description | Request Body |
|---|---|---|---|
| `GET` | `/payment` | List all payments | — |
| `POST` | `/payment` | Create a payment | `{ "orderId": "...", "amount": 99.99, "paymentMethod": "card" }` |
| `GET` | `/payment/:id` | Get a single payment | — |

---

## Getting Started

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- A [MongoDB Atlas](https://www.mongodb.com/atlas) cluster with connection URIs

### Configuration

The MongoDB connection strings are set as environment variables in `docker-compose.yml`. Update the `MONGODB_URI` values for each service before running:

```yaml
environment:
  - MONGODB_URI=mongodb+srv://<user>:<password>@<cluster>/<db-name>
```

> **Note**: Avoid committing real credentials to version control. Use a `.env` file or Docker secrets for production.

### Run with Docker Compose

```bash
# Build and start all services
docker compose up --build

# Run in detached mode
docker compose up --build -d

# Stop all services
docker compose down
```

The API Gateway will be available at **http://localhost:8080**.

### Example Requests

```bash
# Create an item
curl -X POST http://localhost:8080/items \
  -H "Content-Type: application/json" \
  -d '{"name": "Wireless Headphones"}'

# List all orders
curl http://localhost:8080/orders

# Create a payment
curl -X POST http://localhost:8080/payment \
  -H "Content-Type: application/json" \
  -d '{"orderId": "<id>", "amount": 49.99, "paymentMethod": "card"}'
```

---

## Development (without Docker)

To run a single service locally:

```bash
cd item-service   # or order-service / payment-service
npm install
npm run dev
```

The service will start on `http://localhost:3000`.
