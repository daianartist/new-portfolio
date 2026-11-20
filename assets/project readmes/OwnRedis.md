# Own Redis

A lightweight, in-memory key-value database implementation using UDP protocol, inspired by Redis.

## Features

- **UDP Protocol**: Fast, connectionless communication
- **Thread-Safe**: Concurrent access using mutex locks
- **Key Expiration**: Support for time-based key expiration with PX option
- **Commands**: PING, SET, GET with Redis-like syntax
- **Auto-Cleanup**: Background goroutine removes expired keys

## Project Structure

```
├── cmd
│   └── main.go
├── go.mod
├── internal
│   ├── handler
│   │   └── handler.go
│   ├── server
│   │   └── server.go
│   └── store
│       └── store.go
├── own-redis
└── README.md
```

## Installation

```bash
# Clone the repository
git clone <repository-url>
cd own-redis

# Build the project
go build -o own-redis ./cmd 
```

## Usage

### Start the server

```bash
# Default port (8080)
./own-redis

# Custom port
./own-redis --port 9000

# Show help
./own-redis --help
```

### Commands

#### PING
Check if server is running:
```bash
echo "PING" | nc -u 127.0.0.1 8080

# Response: PONG
```

#### SET
Store a key-value pair:
```bash
# Basic SET
echo "SET mykey myvalue" | nc -u 127.0.0.1 8080

# Response: OK

# Multi-word value
echo "SET name John Doe" | nc -u 127.0.0.1 8080

# Response: OK

# SET with expiration (milliseconds)
echo "SET temp data PX 5000" | nc -u 127.0.0.1 8080

# Response: OK (expires after 5 seconds)

# After expiration:
echo "GET temp" | nc -u 127.0.0.1 8080
# (nil)

```

#### GET
Retrieve a value by key:
```bash
echo "GET mykey" | nc -u 127.0.0.1 8080

# Response: myvalue

echo "GET unknown" | nc -u 127.0.0.1 8080

# Response: (nil)
```

## Development

### Running Tests
```bash
# Format code
gofumpt -w .

# Build
go build -o own-redis ./cmd/own-redis

# Run
./own-redis --port 8080
```

### Package Responsibilities

- **cmd/own-redis**: CLI argument parsing and application bootstrap
- **internal/server**: UDP server setup and request/response loop
- **internal/store**: Thread-safe in-memory storage with expiration support
- **internal/handler**: Command parsing and execution logic

## Technical Details

- **Concurrency**: Uses `sync.RWMutex` for thread-safe operations
- **Expiration**: Background cleanup runs every second
- **Protocol**: UDP packets (max 1024 bytes)
- **Case-Insensitive**: All commands are case-insensitive