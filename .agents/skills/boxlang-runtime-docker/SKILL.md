---
name: boxlang-runtime-docker
description: "Use this skill when containerizing BoxLang applications with Docker, including choosing the right image variant, running scripts, the MiniServer web runtime, environment variables, health checks, volume mounts, Docker Compose, and production deployment patterns."
---

# BoxLang with Docker

## Overview

BoxLang ships three official Docker images for different use cases: a headless
CLI image, a MiniServer web runtime, and a MiniServer + Nginx reverse-proxy
image. All images are available in Debian (default) and Alpine (`-alpine`) flavors
and as nightly snapshots (`-snapshot`).

---

## Image Reference

| Image | Purpose | Default port |
|-------|---------|-------------|
| `ortussolutions/boxlang:cli` | Run scripts, REPL, batch jobs | — |
| `ortussolutions/boxlang:miniserver` | Standalone web server | 8080 |
| `ortussolutions/boxlang:miniserver-nginx` | Web server + Nginx reverse proxy | 80 |

### Variants

| Suffix | Description |
|--------|-----------|
| _(none)_ | Debian base |
| `-alpine` | Alpine Linux — smaller image (~50% smaller) |
| `-snapshot` | Latest nightly snapshot build |

---

## CLI Image

### Run a Script

```bash
docker run --rm \
  -v "$(pwd):/scripts" \
  ortussolutions/boxlang:cli \
  boxlang /scripts/myscript.bxs
```

### Open an Interactive REPL

```bash
docker run --rm -it ortussolutions/boxlang:cli boxlang
```

### Pass Variables via CLI

```bash
docker run --rm \
  -e MY_VAR=hello \
  -v "$(pwd):/scripts" \
  ortussolutions/boxlang:cli \
  boxlang /scripts/greet.bxs
```

---

## MiniServer Image

The MiniServer image serves a BoxLang web application from `/app` on port 8080.

### Basic Usage

```bash
docker run --rm \
  -p 8080:8080 \
  -v "$(pwd)/src:/app" \
  ortussolutions/boxlang:miniserver
```

### Install Modules at Startup

```bash
docker run --rm \
  -p 8080:8080 \
  -e "BOXLANG_MODULES=bx-mail bx-pdf" \
  -v "$(pwd)/src:/app" \
  ortussolutions/boxlang:miniserver
```

### Key Environment Variables

| Variable | Description |
|----------|-----------|
| `BOXLANG_MODULES` | Space-delimited list of BoxLang modules to install on startup |
| `BOXLANG_DEBUG` | `true` to enable verbose BoxLang debug output |
| `MINISERVER_JSON` | Override path to the `miniserver.json` config file |
| `JAVA_OPTS` | JVM options (e.g., `-Xmx512m -XX:+UseG1GC`) |
| `MAX_MEMORY` | Maximum JVM heap (e.g., `512m`) — shortcut for `-Xmx` |
| `MIN_MEMORY` | Minimum JVM heap (e.g., `128m`) — shortcut for `-Xms` |

---

## miniserver.json Configuration

Place `miniserver.json` in your `/app` directory (or point to it with
`MINISERVER_JSON`) to customize the server:

```json
{
  "port": 8080,
  "webroot": "/app",
  "host": "0.0.0.0",
  "debug": false,
  "ssl": false
}
```

---

## Health Check

The MiniServer image includes a built-in health check endpoint. Default behavior:

| Setting | Value |
|---------|-------|
| Check interval | 20 seconds |
| Timeout | 30 seconds |
| Retries | 15 |
| URI | `/healthcheck` |

Override the health check URI:

```bash
docker run -e HEALTHCHECK_URI=/status ortussolutions/boxlang:miniserver
```

---

## MiniServer + Nginx Image

The `miniserver-nginx` image bundles BoxLang MiniServer behind Nginx for SSL
termination, caching, and production deployments:

```bash
docker run --rm \
  -p 80:80 \
  -p 443:443 \
  -v "$(pwd)/src:/app" \
  -v "$(pwd)/nginx/conf.d:/etc/nginx/conf.d" \
  ortussolutions/boxlang:miniserver-nginx
```

---

## Dockerfile Examples

### Simple Web Application

```dockerfile
FROM ortussolutions/boxlang:miniserver

# Set environment
ENV BOXLANG_MODULES="bx-mail bx-pdf"
ENV MAX_MEMORY=512m

# Copy application
COPY --chown=boxlang:boxlang src/ /app/

# Copy config
COPY --chown=boxlang:boxlang config/miniserver.json /app/miniserver.json

EXPOSE 8080
```

### Multi-Stage Build (Alpine)

```dockerfile
# Build stage
FROM ortussolutions/boxlang:cli-alpine AS builder

WORKDIR /build
COPY . .

# Run any build steps (compile, test, etc.)
RUN boxlang build.bxs

# Runtime stage
FROM ortussolutions/boxlang:miniserver-alpine

COPY --from=builder /build/dist /app
COPY --chown=boxlang:boxlang miniserver.json /app/miniserver.json

ENV MAX_MEMORY=256m
EXPOSE 8080
```

---

## Docker Compose

```yaml
version: "3.9"

services:
  app:
    image: ortussolutions/boxlang:miniserver
    ports:
      - "8080:8080"
    environment:
      BOXLANG_MODULES: "bx-mail"
      MAX_MEMORY: 512m
      BOXLANG_DEBUG: "false"
    volumes:
      - ./src:/app
      - boxlang-home:/root/.boxlang
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/healthcheck"]
      interval: 20s
      timeout: 10s
      retries: 5
      start_period: 30s
    restart: unless-stopped

  db:
    image: mariadb:11
    environment:
      MARIADB_ROOT_PASSWORD: secret
      MARIADB_DATABASE: myapp
    volumes:
      - db-data:/var/lib/mysql

volumes:
  boxlang-home:
  db-data:
```

---

## Production Tips

- **Use Alpine images** in production (`-alpine`) — significantly smaller footprint
- **Pin to a version tag** (`ortussolutions/boxlang:1.2.3-miniserver`) rather than `latest` for reproducibility
- **Set `MAX_MEMORY`** — BoxLang/JVM defaults may be too low for containers
- **Mount volumes read-only** for application code: `-v $(pwd)/src:/app:ro`
- **Use secrets** via environment variables, not baked into images
- **Enable health checks** and connect to your orchestrator (Kubernetes, ECS, etc.)

---

## Checklist

- [ ] Correct image chosen for use case (CLI / MiniServer / Nginx)
- [ ] `BOXLANG_MODULES` lists all runtime module dependencies
- [ ] JVM memory configured via `MAX_MEMORY` / `MIN_MEMORY` or `JAVA_OPTS`
- [ ] Health check endpoint configured and tested
- [ ] Application secrets injected via runtime env vars (not in image)
- [ ] Image version pinned for production deployments
- [ ] Alpine variant used for production to reduce image size