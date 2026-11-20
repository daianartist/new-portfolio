# Marketflow

Real-Time Market Data Processing System built with Go. This system processes high volumes of cryptocurrency price updates concurrently using Go's concurrency primitives and follows a strict hexagonal architecture.

## Overview

Marketflow simulates a backend system for financial firms that require real-time price updates from multiple exchanges. It supports:
- **Live Mode**: Fetching real-time data from external exchange emulators.
- **Test Mode**: Generating synthetic market data via a local TCP simulation server.
- **Data Aggregation**: Calculating min, max, and average prices per minute.
- **Persistence**: Storing processed stats in PostgreSQL.
- **Caching**: Quick access to the latest prices via Redis.

## Project Structure

```text
.
├── adapters/            # External interface implementations
│   ├── cache/          # Redis caching logic
│   ├── exchange/       # TCP sources (Live and Test Server)
│   ├── storage/        # PostgreSQL persistence
│   └── web/            # REST API handlers
├── application/         # Use cases and business logic orchestration
├── config/              # Configuration loading logic
├── domain/              # Core business models and interfaces
├── main.go              # Application entry point and composition root
└── config.json          # Main configuration file
```

## Features & Architecture

### Hexagonal Architecture
1.  **Domain Layer**: Core models and logic.
2.  **Application Layer**: Use cases, worker pool management, and data aggregation logic.
3.  **Adapters**:
    - **Web**: REST API handlers.
    - **Storage**: PostgreSQL adapter with batch insert support.
    - **Cache**: Redis adapter for latest price caching.
    - **Exchange**: TCP-based data sources (Live and Test).

### Concurrency Implementation
- **Worker Pool**: Each data source is allocated a pool of 5 workers for parallel processing.
- **Fan-in**: Aggregates multiple market data streams into a single processing channel.
- **Fan-out**: Distributes incoming updates across the worker pool.
- **Graceful Shutdown**: Handles termination signals (SIGINT, SIGTERM) to clean up resources.

## Getting Started

### Prerequisites
- Go 1.2+
- PostgreSQL
- Redis

### Quick Start with Docker (Infrastructure)

The project includes a `docker-compose.yml` to spin up PostgreSQL and Redis automatically:

```bash
docker-compose up -d
```

To stop the services:
```bash
docker-compose down
```

### Installation
1.  Clone the repository.
2.  Build the application:
    ```bash
    go build -o marketflow .
    ```

### Configuration
Edit `config.json` to set your server, database, cache, and exchange settings.

### Usage
Run the application:
```bash
./marketflow [--port <N>]
```

View help:
```bash
./marketflow --help
```

> [!TIP]
> If you are using `zsh`, remember to wrap URLs with query parameters in quotes to avoid "no matches found" errors:
> `curl "http://localhost:8080/prices/average/BTCUSDT?period=1m"`

## API Endpoints

### Market Data
- `GET /prices/latest/{symbol}` — Latest price across all exchanges.
- `GET /prices/latest/{exchange}/{symbol}` — Latest price from a specific exchange.
- `GET /prices/highest/{symbol}?period={duration}` — Highest price within a duration (e.g., `1m`, `5m`).
- `GET /prices/lowest/{symbol}?period={duration}` — Lowest price within a duration.
- `GET /prices/average/{symbol}?period={duration}` — Average price within a duration.

### System
- `POST /mode/test` — Switch to Test Mode.
- `POST /mode/live` — Switch to Live Mode.
- `GET /health` — System status and integration health.
