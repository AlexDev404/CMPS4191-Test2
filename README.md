# CMPS4191-Test2

Simple Go WebSocket playground that serves a static test page and exposes a JSON-aware echo service for experimenting with WebSocket message handling patterns.

## Features
- Serves static assets from `web/` and exposes `/test` and `/ws` endpoints.
- Upgrades HTTP GET requests on `/ws` to WebSocket connections using Gorilla WebSocket with strict origin checks for `ws://localhost:4000`.
- Echoes plain text messages with a running counter, supports `UPPER:` and `REVERSE:` directives, and tracks message totals atomically.
- Accepts JSON commands for basic math operations (`add`, `subtract`, `multiply`, `divide`) and responds with structured JSON payloads.
- Maintains healthy connections with ping/pong heartbeats, read deadlines, and graceful close handling.

## Prerequisites
- Go 1.25 or newer (`go env GOVERSION` should report at least go1.25).
- Make (optional) if you want the convenience targets in the provided `Makefile`.

## Setup
1. Clone the repository and enter the project directory.
   ```sh
   git clone https://github.com/alexdev404/ws-main.git
   cd ws-main
   ```
2. Download Go module dependencies.
   ```sh
   go mod download
   ```

## Running the server
- Directly via Go:
  ```sh
  go run ./cmd/web
  ```
- Or with the included Make target:
  ```sh
  make run/web
  ```
The server listens on `http://localhost:4000`. Static files in `web/` are served from the root; `web/test.html` is a ready-to-use client for trying the WebSocket API.

## Using the WebSocket endpoint
1. Start the server.
2. Open `http://localhost:4000/test.html` in a browser. The page establishes a WebSocket connection to `ws://localhost:4000/ws`.
3. Send messages using the input field:
   - Plain text (`hello`) echoes back as `[Msg #N] hello` where `N` is the global counter.
   - Prefix with `UPPER:` to force uppercase (`UPPER:hello` → `[Msg #N] HELLO`).
   - Prefix with `REVERSE:` to reverse the remaining text (`REVERSE:abc` → `[Msg #N] cba`).
   - Send JSON for math commands, e.g. `{"command":"add","a":3,"b":4}`. The server responds with JSON: `{"result":7,"command":"add"}`. Division by zero and unknown commands return an error string.
4. Connections are limited to the allowed origin list; adjust `allowedOrigins` in `internal/ws/handler.go` if you need to serve from a different host.

## Running tests
Execute the Go test suite:
```sh
go test ./...
```

## Project structure
```
cmd/web/          # Entry point for the HTTP/WebSocket server
internal/ws/      # WebSocket handler, message processing helpers
web/              # Static assets including the WebSocket demo page
go.mod            # Module definition and dependencies
Makefile          # Convenience target for running the server
```

## Troubleshooting
- **Origin blocked**: Ensure you access the app through `http://localhost:4000`; otherwise update the `allowedOrigins` slice.
- **Port in use**: Stop other services on port 4000 or change the port in `cmd/web/main.go` and rebuild.
