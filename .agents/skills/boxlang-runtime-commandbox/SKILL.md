---
name: boxlang-runtime-commandbox
description: "Use this skill when deploying BoxLang as an enterprise Java servlet application using CommandBox, configuring server.json for BoxLang, installing BoxLang modules, enabling SSL and rewrites, using BoxLang+ or BoxLang++ subscriptions with CommandBox PRO features, and production server configuration."
---

# BoxLang with CommandBox

## Overview

**CommandBox** is the enterprise Java servlet deployment option for BoxLang. Unlike the MiniServer (Undertow, no-servlet), CommandBox runs BoxLang inside a full Java EE servlet container (Undertow with servlet API), enabling enterprise features like multi-site hosting, SSL management, OS service integration, and JDK management.

| | MiniServer | CommandBox |
|---|---|---|
| Use case | Simple/fast deployment | Enterprise / production |
| Servlet API | No | Yes |
| SSL management | Manual | Built-in + Let's Encrypt |
| Multi-site | No | Yes |
| BoxLang+/++ | Not required | Unlocks PRO features |
| Docker image | `ortussolutions/boxlang` | `ortussolutions/commandbox` |

---

## Installation

### Install CommandBox

```bash
# macOS
brew install commandbox

# Linux (Debian/Ubuntu)
curl -fsSl https://downloads.ortussolutions.com/KEYS | sudo apt-key add -
sudo apt-get install commandbox

# All platforms
curl -fsSl https://downloads.ortussolutions.com/commandbox-cli-install | bash
```

### Install the BoxLang Module (CommandBox < 6.1)

```bash
install commandbox-boxlang
```

> On CommandBox 6.1+, BoxLang support is built-in — no module installation required.

---

## Starting a BoxLang Server

```bash
# One-liner
server start cfengine=boxlang javaVersion=openjdk21_jdk

# With explicit port and webroot
server start cfengine=boxlang port=8080 webroot=/var/www/myapp

# Foreground (don't open browser)
server start cfengine=boxlang openBrowser=false
```

---

## `server.json` Configuration

Place `server.json` in your project root for repeatable server configuration:

### Minimal

```json
{
    "name": "MyBoxLangApp",
    "app": {
        "cfengine": "boxlang",
        "serverHomeDirectory": ".engine/boxlang"
    },
    "openBrowser": false
}
```

### With Rewrites + Modules + SSL

```json
{
    "name": "MyBoxLangApp",
    "app": {
        "cfengine": "boxlang",
        "serverHomeDirectory": ".engine/boxlang"
    },
    "openBrowser": false,
    "web": {
        "rewrites": {
            "enable": true
        },
        "ssl": {
            "enable": true,
            "port": 443,
            "certFile": "/path/to/cert.pem",
            "keyFile": "/path/to/key.pem"
        }
    },
    "JVM": {
        "heapSize": "512m",
        "args": "-XX:+UseG1GC"
    },
    "env": {
        "BOXLANG_ENVIRONMENT": "production",
        "BOXLANG_DEBUG": false
    },
    "scripts": {
        "onServerInitialInstall": "install bx-mail,bx-mysql,bx-redis",
        "onServerStart": "echo 'Server starting...'",
        "onServerStop": "echo 'Server stopping...'"
    }
}
```

### With Custom `boxlang.json`

```json
{
    "app": {
        "cfengine": "boxlang",
        "serverHomeDirectory": ".engine/boxlang",
        "engineConfigFile": ".boxlang.json"
    },
    "web": {
        "rewrites": { "enable": true }
    },
    "scripts": {
        "onServerInitialInstall": "install bx-mail,bx-mysql,bx-compat-cfml"
    }
}
```

---

## Installing BoxLang Modules

Modules are installed from CommandBox and are available after `onServerInitialInstall` runs:

```bash
# Install in running server
install bx-mysql
install bx-mail
install bx-redis
install bx-elasticsearch
install bx-compat-cfml

# Multiple at once
install bx-mysql,bx-mail,bx-redis
```

Or in `server.json`:

```json
"scripts": {
    "onServerInitialInstall": "install bx-mysql,bx-mail,bx-redis"
}
```

---

## Debug Mode

Enable debug mode via any of these methods:

```bash
# CLI flag
server start --debug

# server.json env variable
{
    "env": { "BOXLANG_DEBUG": true }
}
```

Or via `.cfconfig.json`:

```json
{
    "debuggingEnabled": true
}
```

---

## Docker with CommandBox

```bash
# Pull the official image
docker pull ortussolutions/commandbox

# Run BoxLang on CommandBox
docker run -p 8080:8080 \
    -e cfengine=boxlang \
    -v /path/to/myapp:/app \
    ortussolutions/commandbox
```

### Dockerfile

```dockerfile
FROM ortussolutions/commandbox:latest

ENV cfengine=boxlang
ENV PORT=8080

COPY ./ /app
WORKDIR /app

EXPOSE 8080
CMD ["box", "server", "start", "--console"]
```

---

## BoxLang+/++ Subscriptions (CommandBox PRO Features)

A BoxLang+ or BoxLang++ subscription unlocks **CommandBox PRO** enterprise features:

| Feature | BoxLang+ | BoxLang++ |
|---------|----------|-----------|
| Multi-site hosting | ✅ | ✅ |
| Multi-SSL / SNI | ✅ | ✅ |
| OS Service Manager | ✅ | ✅ |
| JDK Management | ✅ | ✅ |
| Monitoring | — | ✅ |
| Priority support | — | ✅ |

Activate via CommandBox:

```bash
server activate --subscription=YOUR_KEY
```

---

## Environment Variables

The servlet/CommandBox runtime honors the same environment variables as the core OS runtime:

| Variable | Description |
|----------|-------------|
| `BOXLANG_DEBUG` | Enable/disable debug mode |
| `BOXLANG_CONFIG` | Path to `boxlang.json` |
| `BOXLANG_HOME` | Override BoxLang home |
| `BOXLANG_ENVIRONMENT` | Environment name (development/staging/production) |

---

## Server Management Commands

```bash
server start         # Start the server
server stop          # Stop the server
server restart       # Restart the server
server status        # View server status
server log           # Tail the server log
server open          # Open in browser
server list          # List all servers
server forget        # Remove server config
```

---

## Checklist

- [ ] Use `server.json` for repeatable, version-controlled server config
- [ ] Set `serverHomeDirectory` to keep engine files out of source tree
- [ ] Use `scripts.onServerInitialInstall` to auto-install required modules
- [ ] Provide a custom `engineConfigFile` (`.boxlang.json`) for project-level BoxLang config
- [ ] Use `JVM.heapSize` to set appropriate memory limits for production
- [ ] Enable `web.rewrites.enable: true` for front-controller or SPA routing
- [ ] Prefer `env` vars in `server.json` over hard-coded values for environment-specific settings
- [ ] For Docker, use `ortussolutions/commandbox` with `cfengine=boxlang` env var