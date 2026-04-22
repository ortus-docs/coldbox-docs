---
name: boxlang-runtime-miniserver
description: "Use this skill when running BoxLang as a lightweight web server using the BoxLang MiniServer (Undertow-based), configuring miniserver.json, using the .boxlang.json project convention, enabling health checks, warmup URLs, environment variable overrides, and tuning for development and production."
---

# BoxLang MiniServer

## Overview

BoxLang MiniServer is the official lightweight web server for BoxLang applications. It is built on **Undertow** and is NOT a servlet container — it is a purpose-built, fast web runtime for BoxLang. Use it for development servers and production deployments where a full Java EE container is not needed.

Binary: `boxlang-miniserver` (Windows: `boxlang-miniserver.bat`)
Default behavior: binds `0.0.0.0:8080`, uses current working directory as webroot.

---

## Quick Start

```bash
# Start in current directory on default port 8080
boxlang-miniserver

# Custom port and webroot
boxlang-miniserver --port 9000 --webroot ./public

# Enable URL rewrites (SPA or front-controller pattern)
boxlang-miniserver --rewrites

# Production with health checks
boxlang-miniserver --port 8080 --health-check --health-check-secure
```

---

## CLI Flags

| Flag | Short | Description |
|------|-------|-------------|
| `--port <n>` | `-p` | Port to bind (default: 8080) |
| `--host <ip>` | | Host/IP to bind (default: `0.0.0.0`) |
| `--webroot <path>` | `-w` | Webroot directory (default: CWD) |
| `--debug` | `-d` | Enable debug mode |
| `--rewrites` | `-r` | Enable URL rewrites to `index.bxm` |
| `--configPath <path>` | `-c` | Path to JSON config file |
| `--serverHome <path>` | `-s` | Override server home directory |
| `--health-check` | | Enable health check endpoints |
| `--health-check-secure` | | Secure health checks (details localhost-only) |
| `--version` | | Print version |
| `--help` | | Print help |

---

## Environment Variables

Environment variables are scanned **before** CLI arguments; CLI args take precedence.

| Variable | Description |
|----------|-------------|
| `BOXLANG_CONFIG` | Override `boxlang.json` path |
| `BOXLANG_DEBUG` | Enable/disable debug mode |
| `BOXLANG_HOME` | Override server HOME directory |
| `BOXLANG_HOST` | Override bind host |
| `BOXLANG_PORT` | Override bind port |
| `BOXLANG_REWRITES` | Enable/disable URL rewrites |
| `BOXLANG_REWRITE_FILE` | Rewrite target file (default: `index.bxm`) |
| `BOXLANG_WEBROOT` | Override webroot path |
| `BOXLANG_HEALTH_CHECK` | Enable health check endpoints |
| `BOXLANG_HEALTH_CHECK_SECURE` | Enable secure health checks |
| `BOXLANG_MINISERVER_OPTS` | Extra JVM options for startup |

---

## JSON Configuration (`miniserver.json`)

Create a `miniserver.json` file and pass it with `--configPath` (or use `.boxlang.json` project convention):

```json
{
    "port": 8080,
    "host": "0.0.0.0",
    "webRoot": "./www",
    "debug": false,
    "configPath": "./.boxlang.json",
    "serverHome": "./.engine/boxlang",
    "rewrites": true,
    "rewriteFileName": "index.bxm",
    "healthCheck": true,
    "healthCheckSecure": true,
    "envFile": "./.env",
    "warmupURLs": ["/api/warmup", "/health/ready"],
    "undertow": {
        "ioThreads": 4,
        "workerThreads": 32,
        "bufferSize": 16384
    },
    "socket": {
        "tcpNoDelay": true,
        "reuseAddress": true
    },
    "websocket": {
        "maxFrameSize": 65536,
        "maxTextMessageSize": 65536
    }
}
```

### Config Priority (high → low)

1. CLI arguments
2. JSON config file
3. Environment variables
4. Defaults

---

## `.boxlang.json` Project Convention

Place a `.boxlang.json` in your webroot for project-level BoxLang configuration. It is automatically merged with the global `boxlang.json` at startup — commit it to source control for portable config.

```json
{
    "modules": {
        "bx-mysql": { "enabled": true },
        "bx-mail":  { "enabled": true }
    },
    "debugMode": false,
    "timezone": "UTC"
}
```

Pair with a `miniserver.json` that points to it:

```json
{
    "port": 8080,
    "webRoot": "./www",
    "configPath": "./.boxlang.json"
}
```

---

## Example Configurations

### Development

```json
{
    "port": 8080,
    "webRoot": "./public",
    "debug": true,
    "rewrites": true
}
```

### Production

```json
{
    "port": 8080,
    "host": "0.0.0.0",
    "webRoot": "/var/www/myapp",
    "rewrites": true,
    "healthCheck": true,
    "healthCheckSecure": true,
    "warmupURLs": ["/api/warmup/database", "/health/ready"],
    "undertow": {
        "ioThreads": 8,
        "workerThreads": 64
    }
}
```

### Full Options

```json
{
    "port": 8080,
    "host": "0.0.0.0",
    "webRoot": "./www",
    "debug": false,
    "rewrites": true,
    "rewriteFileName": "index.bxm",
    "serverHome": "./.engine/boxlang",
    "configPath": "./.boxlang.json",
    "envFile": ".env",
    "healthCheck": true,
    "healthCheckSecure": true,
    "warmupURLs": ["/api/warmup"],
    "undertow": { "ioThreads": 4, "workerThreads": 32, "bufferSize": 16384 },
    "socket": { "tcpNoDelay": true, "reuseAddress": true },
    "websocket": { "maxFrameSize": 65536, "maxTextMessageSize": 65536 }
}
```

---

## Health Check Endpoints

Enable with `--health-check`. With `--health-check-secure`, remote callers get minimal info only.

| Endpoint | Description |
|----------|-------------|
| `/health` | Full JSON health info (status, uptime, JVM, memory) |
| `/health/ready` | Readiness probe — simple UP/DOWN |
| `/health/live` | Liveness probe — simple UP/DOWN |

```json
{
    "status": "UP",
    "timestamp": "2025-08-01T17:05:47Z",
    "uptime": "1m 47s",
    "uptimeMs": 107245,
    "version": "1.4.0",
    "javaVersion": "17.0.2",
    "memoryUsed": 152093696,
    "memoryMax": 4294967296
}
```

---

## Environment Files (`.env`)

The MiniServer auto-loads `.env` from the webroot at startup. Variables are added to Java System Properties and accessible via `getSystemSetting()`:

```bash
# .env
DATABASE_URL=jdbc:mysql://localhost:3306/mydb
API_KEY=secret
DEBUG_MODE=true
```

```js
// Works in both local (.env) and production (real env var)
var apiKey = getSystemSetting( "API_KEY" )
var dbUrl  = getSystemSetting( "DATABASE_URL" )
```

> Note: Use `getSystemSetting()` rather than `server.system.environment` — it works for both `.env` (system properties) and real environment variables.

---

## Warmup URLs

Pre-request URLs on startup to warm caches and connections before serving traffic:

```json
{
    "warmupURLs": [
        "/api/warmup/database",
        "/api/warmup/cache",
        "/health/ready"
    ]
}
```

Warmup runs sequentially after server init. Failures are logged but do not abort startup.

---

## Security Features

- **Hidden file protection** — any file or directory starting with `.` returns `404` automatically and cannot be disabled
- `.env` files are blocked from HTTP access even when `healthCheck` is enabled
- Health check endpoints never expose environment values

---

## Undertow Tuning

| Setting | Default | Description |
|---------|---------|-------------|
| `undertow.ioThreads` | CPU count | I/O event threads |
| `undertow.workerThreads` | `ioThreads * 8` | Worker thread pool size |
| `undertow.bufferSize` | 16384 | Buffer size in bytes |
| `socket.tcpNoDelay` | `true` | Disable Nagle algorithm |
| `socket.reuseAddress` | `true` | Allow address reuse on restart |
| `websocket.maxFrameSize` | 65536 | Max WebSocket frame size |
| `websocket.maxTextMessageSize` | 65536 | Max text message size |

---

## Checklist

- [ ] Use `.boxlang.json` in webroot for portable project-level config
- [ ] Enable `--rewrites` for SPA or front-controller apps (routes to `index.bxm`)
- [ ] Use `getSystemSetting()` to read env vars (works with both `.env` and real env vars)
- [ ] Enable `--health-check --health-check-secure` for container/load-balancer readiness probes
- [ ] Add `warmupURLs` for production to pre-load DB connections and caches
- [ ] Tune `undertow.workerThreads` based on expected concurrency
- [ ] Never rely on direct env var access for `.env`-sourced values