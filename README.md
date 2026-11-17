# MuzzServer

[![codecov](https://codecov.io/gh/MuzikGroup/MuzzServer/graph/badge.svg)](https://codecov.io/gh/MuzikGroup/MuzzServer)

A high performance Go WebSocket server for Muzik's "Listen Together" feature.  
Utilizes protobuf and gzip compression for fast and efficient communication between clients.

**Developer:** [Ignatius](https://github.com/Motius)

# Quickstart

## Locally
You need to install go, protobuf, and protoc-gen-go

```bash
git clone https://github.com/MuzikGroup/MuzzServer
cd muzzserver

# Generate protobuf files (required first time)
./scripts/generate_proto.sh

# Download dependencies
go mod download

# Build the server
go build -o muzzserver ./cmd/muzzserver

# Run on default port 8080
./muzzserver

# Run on custom port
PORT=9000 ./muzzserver
```

## Configuration

`PORT` sets the HTTP/WebSocket port. It defaults to `8080`.

The server writes graceful-shutdown recovery state to `server_state.json` with `0600` permissions.

## Project Structure

- `cmd/muzzserver` contains the executable entrypoint.
- `internal/server` contains server behavior and tests.
- `muzzproto` contains the protobuf schema submodule.
- `proto` contains generated Go protobuf code.
- `scripts` contains development and generation scripts.

## Docker

```bash
# Clone the repository
git clone https://github.com/MuzikGroup/MuzzServer
cd muzzserver

# Build locally
docker build -t MuzikGroup:latest .

# Run on port 8080
docker run -d \
  -p 8080:8080 \
  -e PORT=8080 \
  --name muzzserver \
  muzzserver:latest

# Run on custom port
docker run -d \
  -p 9000:9000 \
  -e PORT=9000 \
  --name muzzserver \
  muzzserver:latest
```

## Docker Compose

```yaml
---
services:
  muzzserver:
    image: ghcr.io/MuzikGroup/muzzserver:latest
    ports:
      - "8080:8080"
    environment:
      - PORT=8080
    healthcheck:
      test:
        [
          "CMD",
          "wget",
          "--no-verbose",
          "--tries=1",
          "--spider",
          "http://localhost:8080/health",
        ]
      interval: 30s
      timeout: 3s
      retries: 3
      start_period: 5s
    restart: unless-stopped
```
