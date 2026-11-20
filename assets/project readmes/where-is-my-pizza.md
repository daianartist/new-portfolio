# wheres-my-pizza

A distributed restaurant order management system built with Go. The project demonstrates microservices architecture with asynchronous communication via RabbitMQ and persistent storage in PostgreSQL.

## Overview

The system simulates a real restaurant workflow:

1. A customer places an order through the **Order Service** (HTTP API).
2. The order is saved to PostgreSQL and published to RabbitMQ.
3. A **Kitchen Worker** picks up the order, simulates cooking, and updates the status.
4. A **Notification Subscriber** receives status updates and prints them to the console.

```
HTTP Client
    |
    v
Order Service -----> PostgreSQL
    |
    v
RabbitMQ (orders_topic)
    |
    v
Kitchen Worker -----> PostgreSQL
    |
    v
RabbitMQ (notifications_fanout)
    |
    v
Notification Subscriber
```

## Tech Stack

- **Go** — all services in a single binary
- **PostgreSQL** — order and worker data (`pgx/v5`)
- **RabbitMQ** — message broker (`amqp091-go`)
- **YAML** — configuration (`config.yaml`)

## Prerequisites

- Go 1.24+
- Docker Desktop (recommended)

## Quick Start

### 1. Start infrastructure

```bash
docker compose up -d
```

This starts:

| Service    | Port  | Credentials                          |
|------------|-------|--------------------------------------|
| PostgreSQL | 5432  | `restaurant_user` / `restaurant_pass` |
| RabbitMQ   | 5672  | `guest` / `guest`                    |
| RabbitMQ UI| 15672 | http://localhost:15672               |

### 2. Build

```bash
go build -o restaurant-system .
```

### 3. Run services

Open separate terminals for each service:

```bash
# Terminal 1 — Order Service
./restaurant-system --mode=order-service --port=3000

# Terminal 2 — Kitchen Worker
./restaurant-system --mode=kitchen-worker --worker-name=chef_anna

# Terminal 3 — Notification Subscriber
./restaurant-system --mode=notification-subscriber
```

### 4. Place an order

```bash
curl -X POST http://localhost:3000/orders \
  -H "Content-Type: application/json" \
  -d '{
    "customer_name": "Jane Doe",
    "order_type": "takeout",
    "items": [
      {"name": "Margherita Pizza", "quantity": 1, "price": 15.99},
      {"name": "Caesar Salad", "quantity": 1, "price": 8.99}
    ]
  }'
```

Example response:

```json
{
  "order_number": "ORD_20260607_001",
  "status": "received",
  "total_amount": 24.98
}
```

After ~10 seconds you should see notifications like:

```
Notification for order ORD_20260607_001: Status changed from 'received' to 'cooking' by chef_anna.
Notification for order ORD_20260607_001: Status changed from 'cooking' to 'ready' by chef_anna.
```

## Services

| Mode                      | Description                                      | Port  |
|---------------------------|--------------------------------------------------|-------|
| `order-service`           | HTTP API for creating orders                     | 3000  |
| `kitchen-worker`          | Processes orders from RabbitMQ                   | —     |
| `notification-subscriber` | Prints order status updates                      | —     |
| `tracking-service`        | Read-only API for order and worker status        | 3002  |

### Order Service

```bash
./restaurant-system --mode=order-service --port=3000 --max-concurrent=50
```

**Endpoint:** `POST /orders`

### Kitchen Worker

```bash
# General worker (handles all order types)
./restaurant-system --mode=kitchen-worker --worker-name=chef_anna --prefetch=1

# Specialized worker (only dine-in orders)
./restaurant-system --mode=kitchen-worker --worker-name=chef_mario --order-types=dine_in
```

Cooking simulation times:

| Order Type | Duration |
|------------|----------|
| `dine_in`  | 8 sec    |
| `takeout`  | 10 sec   |
| `delivery` | 12 sec   |

### Notification Subscriber

```bash
./restaurant-system --mode=notification-subscriber
```

## Configuration

Edit `config.yaml` in the project root:

```yaml
database:
  host: localhost
  port: 5432
  user: restaurant_user
  password: restaurant_pass
  database: restaurant_db

rabbitmq:
  host: localhost
  port: 5672
  user: guest
  password: guest
```

## Project Structure

```
.
├── main.go                     # Entry point, service mode switch
├── config.yaml                 # Database and RabbitMQ settings
├── docker-compose.yml          # Local Postgres + RabbitMQ
├── migrations/
│   └── 001_init.sql            # Database schema
└── internal/
    ├── config/                 # YAML config loader
    ├── db/                     # PostgreSQL connection and migrations
    ├── logger/                 # Structured JSON logging
    ├── models/                 # Shared data types
    ├── rabbitmq/               # RabbitMQ client and queue setup
    ├── order/                  # Order Service
    ├── kitchen/                # Kitchen Worker
    └── notification/           # Notification Subscriber
```

## Logging

All services write structured JSON logs to stdout:

```json
{
  "timestamp": "2026-06-07T13:00:35.815545Z",
  "level": "INFO",
  "service": "order-service",
  "action": "service_started",
  "message": "Order Service started on port 3000",
  "hostname": "MacBook-Pro-Akerke.local",
  "request_id": "startup-001"
}
```

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.