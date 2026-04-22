---
name: boxlang-runtime-digitalocean-app
description: "Use this skill when deploying BoxLang applications to DigitalOcean App Platform using the official BoxLang starter kit, setting up auto-deployment from GitHub, and understanding the MiniServer + multi-stage Docker build architecture used in the starter."
---

# BoxLang on DigitalOcean App Platform

## Overview

DigitalOcean App Platform supports BoxLang applications via a containerized starter kit. The starter uses BoxLang MiniServer inside a multi-stage Docker build and auto-deploys from GitHub on every push to `main`.

---

## Quick Start

### 1. Get the Starter Kit

```
https://github.com/ortus-boxlang/boxlang-starter-digitalocean
```

Fork or clone the repository:

```bash
git clone https://github.com/ortus-boxlang/boxlang-starter-digitalocean.git myapp
cd myapp
```

### 2. One-Click Deploy

Use the "Deploy to DigitalOcean" button in the starter kit README to create the App Platform app automatically.

### 3. Connect Your Fork

In the DigitalOcean App Platform dashboard:
1. Create a new App
2. Connect your forked GitHub repository
3. Select the `main` branch
4. App Platform detects the Dockerfile and builds automatically

Every push to `main` triggers a new deployment (2–3 minutes build time).

---

## Architecture

The starter uses a **multi-stage Docker build**:

```dockerfile
# Stage 1: Build dependencies
FROM ortussolutions/boxlang:latest AS builder
WORKDIR /app
COPY . .
RUN box install --production

# Stage 2: Runtime image
FROM ortussolutions/boxlang:miniserver
WORKDIR /app
COPY --from=builder /app /app
EXPOSE 8080
CMD ["boxlang-miniserver", "--port", "8080", "--webroot", "/app/www"]
```

| Component | Technology |
|-----------|-----------|
| Web server | BoxLang MiniServer (Undertow) |
| Build | Multi-stage Docker |
| Platform | DigitalOcean App Platform |
| Port | 8080 (App Platform maps to 443/80) |
| Build time | 2–3 minutes |

---

## Environment Variables

Configure your app in the DigitalOcean dashboard under **Settings → Environment Variables**:

```bash
DATABASE_URL=...
API_KEY=...
BOXLANG_DEBUG=false
```

Access in BoxLang with `getSystemSetting()`:

```js
var dbUrl = getSystemSetting( "DATABASE_URL" )
var apiKey = getSystemSetting( "API_KEY" )
```

---

## Auto-Deploy Workflow

```text
1. Fork starter → connect to App Platform
2. Develop locally with: boxlang-miniserver --port 8080 --webroot ./www
3. Push to main → App Platform builds + deploys automatically
4. Monitor build logs in the DigitalOcean dashboard
```

---

## Local Development

```bash
# Install BoxLang MiniServer
# (see miniserver skill)

# Run locally using the same config as production
boxlang-miniserver --port 8080 --webroot ./www

# Or with Docker (mirrors production exactly)
docker build -t myapp .
docker run -p 8080:8080 myapp
```

---

## Checklist

- [ ] Fork `boxlang-starter-digitalocean` (don't clone into a new repo — fork to keep deploy button working)
- [ ] Connect GitHub fork to DigitalOcean App Platform
- [ ] Set environment variables in App Platform dashboard (not in source code)
- [ ] Use `getSystemSetting("KEY")` for environment variable access in BoxLang code
- [ ] Test locally with `boxlang-miniserver` before pushing to trigger a deploy
- [ ] Check build logs in App Platform if deployment fails (builds take 2–3 min)