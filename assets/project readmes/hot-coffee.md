# ☕ hot-coffee

> A modern coffee shop management system built with Go

##  Overview

**hot-coffee** is a sophisticated coffee shop management system that simulates the backend operations of a real coffee shop. Built with clean architecture principles, it provides a RESTful API for managing orders, menu items, and inventory—all while maintaining data integrity and operational efficiency.

This system handles everything from order creation and fulfillment to inventory tracking and sales analytics, giving you hands-on experience with production-grade software architecture.

## ✨ Features

-  **Order Management** - Create, update, close, and delete customer orders
-  **Menu System** - Manage menu items with prices, descriptions, and ingredient requirements
-  **Inventory Control** - Track ingredient stock levels and automatic deduction on order fulfillment
-  **Sales Analytics** - Generate reports on total sales and popular items
-  **RESTful API** - Clean HTTP endpoints following REST principles
-  **Comprehensive Logging** - Built-in logging using Go's `log/slog` package
-  **Layered Architecture** - Separation of concerns with handler, service, and data layers

## 🏛️ Architecture

The application follows a three-layered architecture pattern:

```
┌─────────────────────────────────────┐
│     Presentation Layer (Handlers)   │
│   - HTTP request/response handling  │
│   - Input validation & formatting   │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│    Business Logic Layer (Services)  │
│   - Core business rules             │
│   - Data processing                 │
│   - Aggregations & computations     │
└─────────────────┬───────────────────┘
                  │
┌─────────────────▼───────────────────┐
│   Data Access Layer (Repositories)  │
│   - JSON file operations            │
│   - Data persistence                │
│   - Data integrity management       │
└─────────────────────────────────────┘
```

##  Project Structure

```
hot-coffee/
├── cmd/
│   └── main.go                 # Application entry point
├── internal/
│   ├── handler/                # HTTP handlers (Presentation Layer)
│   │   ├── order_handler.go
│   │   ├── menu_handler.go
│   │   └── inventory_handler.go
│   ├── service/                # Business logic (Service Layer)
│   │   ├── order_service.go
│   │   ├── menu_service.go
│   │   └── inventory_service.go
│   └── dal/                    # Data access (Repository Layer)
│       ├── order_repository.go
│       ├── menu_repository.go
│       └── inventory_repository.go
├── models/                     # Data models
│   ├── order.go
│   ├── menu_item.go
│   └── inventory_item.go
├── data/                       # JSON data storage
│   ├── orders.json
│   ├── menu_items.json
│   └── inventory.json
├── go.mod
└── README.md
```

##  Getting Started

### Prerequisites

- Go 1.21 or higher
- Basic understanding of REST APIs and JSON

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/akkassymb/hot-coffee.git
   cd hot-coffee
   ```

2. **Build the application**
   ```bash
   go build -o hot-coffee .
   ```

3. **Run the server**
   ```bash
   ./hot-coffee --port 8080 --dir ./data
   ```

### Usage

```bash
Coffee Shop Management System

Usage:
  hot-coffee [--port <N>] [--dir <S>] 
  hot-coffee --help

Options:
  --help       Show this screen.
  --port N     Port number (default: 8080)
  --dir S      Path to the data directory (default: ./data)
```

## 📡 API Endpoints

### Orders

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/orders` | Create a new order |
| `GET` | `/orders` | Get all orders |
| `GET` | `/orders/{id}` | Get specific order |
| `PUT` | `/orders/{id}` | Update an order |
| `DELETE` | `/orders/{id}` | Delete an order |
| `POST` | `/orders/{id}/close` | Close an order |

### Menu Items

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/menu` | Add new menu item |
| `GET` | `/menu` | Get all menu items |
| `GET` | `/menu/{id}` | Get specific menu item |
| `PUT` | `/menu/{id}` | Update menu item |
| `DELETE` | `/menu/{id}` | Delete menu item |

### Inventory

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/inventory` | Add inventory item |
| `GET` | `/inventory` | Get all inventory |
| `GET` | `/inventory/{id}` | Get specific item |
| `PUT` | `/inventory/{id}` | Update inventory |
| `DELETE` | `/inventory/{id}` | Delete inventory item |

### Reports

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/reports/total-sales` | Get total sales amount |
| `GET` | `/reports/popular-items` | Get popular menu items |

##  Example Requests

### Create an Order

```bash
curl -X POST http://localhost:8080/orders \
  -H "Content-Type: application/json" \
  -d '{
    "customer_name": "John Doe",
    "items": [
      {
        "product_id": "latte",
        "quantity": 2
      },
      {
        "product_id": "croissant",
        "quantity": 1
      }
    ]
  }'
```

### Add Menu Item

```bash
curl -X POST http://localhost:8080/menu \
  -H "Content-Type: application/json" \
  -d '{
    "product_id": "cappuccino",
    "name": "Cappuccino",
    "description": "Espresso with foamed milk",
    "price": 4.00,
    "ingredients": [
      {
        "ingredient_id": "espresso_shot",
        "quantity": 1
      },
      {
        "ingredient_id": "milk",
        "quantity": 150
      }
    ]
  }'
```

##  Key Features Explained

### Automatic Inventory Management

When an order is created, the system:
1. Validates ingredient availability
2. Checks sufficient stock for all items
3. Deducts ingredients from inventory upon order fulfillment
4. Returns detailed error messages if stock is insufficient

### Data Persistence

All data is stored in JSON files:
- **orders.json** - Customer orders and their status
- **menu_items.json** - Available products with ingredients
- **inventory.json** - Ingredient stock levels

### Error Handling

The API returns appropriate HTTP status codes:
- `200 OK` - Successful GET requests
- `201 Created` - Successful POST requests
- `400 Bad Request` - Invalid input
- `404 Not Found` - Resource not found
- `500 Internal Server Error` - Server errors

## 🛠️ Development

### Code Standards

- Code must be formatted with `gofumpt`
- Only built-in Go packages are allowed
- Follow SOLID principles
- Implement comprehensive error handling
- Use `log/slog` for all logging

### Testing

```bash
go build -o hot-coffee ./...
./hot-coffee
```

## 📚 Learning Objectives

This project teaches:
-  REST API design and implementation
-  JSON data handling
-  Layered software architecture
-  SOLID design principles
-  Logging best practices
-  Error handling and validation
-  File-based data persistence


## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request


## 👨‍💻 Author

- GitHub: [@akkassymb](https://github.com/akkassymb)
- GitHub: [@daarapbek](https://github.com/daarapbek)
