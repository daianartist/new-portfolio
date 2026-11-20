# triple-s — Simple Storage Service

A lightweight S3-like storage server implemented in Go using only the standard library.  
The server provides REST-style API endpoints to manage **buckets** and **objects**, storing all data and metadata on disk.

---

## Features

### Bucket Management
- Create a bucket (`PUT /{BucketName}`)
- List all buckets (`GET /`)
- Delete a bucket (`DELETE /{BucketName}`)

### Object Operations
- Upload an object (`PUT /{BucketName}/{ObjectKey}`)
- Retrieve an object (`GET /{BucketName}/{ObjectKey}`)
- Delete an object (`DELETE /{BucketName}/{ObjectKey}`)

### Metadata Storage
- `buckets.csv` — metadata of all buckets  
- Each bucket has its own `objects.csv`  
- Objects stored directly on disk

---

## Requirements

- Must compile using:

```bash
go build -o triple-s .
```

## Usage
Run the server
```bash
./triple-s -port 9000 -dir ./data
```

Show help
```bash
./triple-s --help
```

Help output
```bash
Simple Storage Service.

Usage:
    triple-s [-port <N>] [-dir <S>]
    triple-s --help

Options:
    --help       Show this screen.
    --port N     Port number
    --dir S      Path to the directory for project data

```

## Create bucket
```bash
curl -X PUT http://localhost:8080/photos
```
## Upload object
```bash
curl -X PUT -H "Content-Type: image/png" \
  --data "fake-image-data" \
  http://localhost:8080/photos/sunset.png
```
## Retrieve object
```bash
curl http://localhost:8080/photos/sunset.png
```
## Delete object
```bash
curl -X DELETE http://localhost:8080/photos/sunset.png
```
## Delete bucket
```bash
curl -X DELETE http://localhost:8080/photos
```