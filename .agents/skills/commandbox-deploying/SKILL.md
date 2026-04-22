---
name: commandbox-deploying
description: "Use this skill for deploying CommandBox applications to production: Docker with ortussolutions/commandbox image, environment variables for Docker, GitHub Actions with setup-commandbox action, Heroku/Dokku buildpack, Amazon Lightsail setup, starting as OS service, using server.json for repeatable deployments, and CFConfig for engine configuration."
---

# Deploying CommandBox

## Overview

CommandBox can deploy CFML/BoxLang applications to production using its embedded server or via container-based deployments. The `server.json` file is the cornerstone of reproducible deployments.

---

## Production `server.json`

Create a `server.json` in your project root for repeatable deployments:

```json
{
    "name": "my-app-production",
    "openBrowser": false,
    "profile": "production",
    "web": {
        "webroot": "www",
        "host": "0.0.0.0",
        "bindings": {
            "HTTP": { "listen": "8080" },
            "SSL": {
                "listen": 8443,
                "certFile": "/certs/server.crt",
                "keyFile": "/certs/server.key"
            }
        },
        "rewrites": {
            "enable": true
        },
        "blockCFAdmin": "external",
        "blockSensitivePaths": true
    },
    "app": {
        "cfengine": "lucee@5",
        "serverHomeDirectory": "/var/cfml/engine"
    },
    "jvm": {
        "heapSize": "512m",
        "maxHeapSize": "2G",
        "args": ["-XX:+UseG1GC"]
    },
    "env": {
        "ENVIRONMENT": "production"
    }
}
```

```bash
# Start production server
server start

# Start without opening browser
server start --noOpenBrowser

# Start in console mode (foreground, good for Docker)
server start --console
```

---

## Docker

### Official Image

```bash
# Pull the official CommandBox image
docker pull ortussolutions/commandbox

# Basic run
docker run -p 8080:8080 -v "/path/to/your/app:/app" ortussolutions/commandbox

# With SSL
docker run \
    -p 8080:8080 \
    -p 8443:8443 \
    -v "/path/to/your/app:/app" \
    ortussolutions/commandbox

# Custom ports via env vars
docker run \
    -e PORT=80 \
    -e SSL_PORT=443 \
    --expose 80 \
    --expose 443 \
    -v "/path/to/your/app:/app" \
    ortussolutions/commandbox
```

### Docker Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | `8080` | HTTP port to serve on |
| `SSL_PORT` | `8443` | HTTPS port to serve on |
| `CFENGINE` | — | CFML engine (e.g., `lucee@5`, `adobe@2023`) |
| `HEALTHCHECK_URI` | `http://127.0.0.1:${PORT}/` | Health check endpoint |
| `SERVER_HOME_DIRECTORY` | `/root/serverHome` | Server home path |
| `cfconfig_<setting>` | — | Any CFConfig setting (e.g., `cfconfig_adminPassword`) |
| `cfconfigfile` | — | Path to a CFConfig JSON file to apply |
| `box_config_*` | — | CommandBox config overrides |

```bash
docker run \
    -e PORT=8080 \
    -e CFENGINE=lucee@5 \
    -e cfconfig_adminPassword=mySecret \
    -e cfconfig_requestTimeout="0,0,5,0" \
    -v "/my/app:/app" \
    ortussolutions/commandbox
```

### `Dockerfile` Example

```dockerfile
FROM ortussolutions/commandbox:latest

# Copy application
COPY . /app

# Install dependencies
RUN cd /app && box install --production

# Expose ports
EXPOSE 8080 8443

# CommandBox handles startup automatically
```

### `docker-compose.yml` Example

```yaml
version: "3.8"
services:
  app:
    image: ortussolutions/commandbox:latest
    ports:
      - "8080:8080"
      - "8443:8443"
    volumes:
      - ./:/app
    environment:
      PORT: 8080
      CFENGINE: "lucee@5"
      cfconfig_adminPassword: "${CFML_ADMIN_PASSWORD}"
      cfconfig_datasources: '{"myDB":{"database":"mydb","dbdriver":"MySQL","host":"db","port":3306,"username":"root","password":"root"}}'
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 5
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: mydb
```

---

## GitHub Actions

### Official Action

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup CommandBox
        uses: Ortus-Solutions/setup-commandbox@v2.0.0

      - name: Install Dependencies
        run: box install

      - name: Start Server
        run: box server start --noOpenBrowser

      - name: Run Tests
        run: box testbox run

  deploy:
    runs-on: ubuntu-latest
    needs: test
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4

      - name: Setup CommandBox with ForgeBox Token
        uses: Ortus-Solutions/setup-commandbox@v2.0.0
        with:
          forgeboxAPIKey: ${{ secrets.FORGEBOX_API_KEY }}
          installSystemModules: true

      - name: Build & Deploy
        run: |
          box install --production
          box task run Workbench.build
```

### Action Inputs

| Input | Type | Default | Description |
|-------|------|---------|-------------|
| `forgeboxAPIKey` | string | — | ForgeBox API token |
| `installSystemModules` | boolean | `false` | Installs `commandbox-cfconfig` and `commandbox-dotenv` |
| `install` | string | — | Comma-delimited list of packages to install |
| `warmup` | boolean | `false` | Run box binary warmup |
| `version` | semver | `latest` | CommandBox version to install |

```yaml
# Install specific version
- uses: Ortus-Solutions/setup-commandbox@v2.0.0
  with:
    version: 6.3.2

# Install extra modules
- uses: Ortus-Solutions/setup-commandbox@v2.0.0
  with:
    install: commandbox-fusionreactor,commandbox-acme
```

---

## Heroku / Dokku

### Heroku Setup

```bash
# Create Heroku app
heroku apps:create my-cfml-app

# Set Ortus buildpack
heroku buildpacks:set https://github.com/ortus-solutions/heroku-buildpack-commandbox.git

# Add git remote
git remote add heroku https://git.heroku.com/my-cfml-app.git

# Deploy
git push heroku main
```

### Dokku Setup

Create `.buildpacks` in your project root:

```
https://github.com/ortus-solutions/heroku-buildpack-commandbox.git
```

```bash
# Add Dokku remote
git remote add dokku dokku@dokku.mydomain.com:my-cfml-app

# Deploy
git push dokku main

# Deploy specific branch
git push dokku mybranch:main
```

---

## Amazon Lightsail / VPS Bootstrap

Full cloud-init / launch script for Ubuntu:

```bash
#!/bin/bash
set -e

# 1. Update and install Java
sudo apt-get update -y
sudo apt-get install -y openjdk-21-jre libappindicator3-dev

# 2. Install CommandBox
curl -fsSl https://downloads.ortussolutions.com/debs/gpg | sudo apt-key add -
echo "deb https://downloads.ortussolutions.com/debs/noarch /" | sudo tee /etc/apt/sources.list.d/commandbox.list
sudo apt-get update && sudo apt-get install -y commandbox

# 3. Clone your app
sudo git clone https://github.com/your-org/your-app.git /app

# 4. Install dependencies
cd /app && sudo box install --production

# 5. Start server (background, persistent)
cd /app && sudo box server start --console &
```

---

## Starting as an OS Service (systemd)

```ini
# /etc/systemd/system/commandbox.service
[Unit]
Description=CommandBox CFML Server
After=network.target

[Service]
Type=simple
User=www-data
WorkingDirectory=/var/www/myapp
ExecStart=/usr/bin/box server start --console
ExecStop=/usr/bin/box server stop
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable commandbox
sudo systemctl start commandbox
sudo systemctl status commandbox
```

---

## CFConfig Integration

[CFConfig](https://cfconfig.ortusbooks.com) is a CommandBox module for managing CFML engine settings:

```bash
# Install cfconfig module
install commandbox-cfconfig

# Apply settings to running server
cfconfig set adminPassword=mySecret
cfconfig set requestTimeout=0,0,5,0
cfconfig set datasources='{"myDB":{"dbdriver":"MySQL","host":"localhost"}}'

# Export settings to JSON
cfconfig export myconfig.json

# Import settings
cfconfig import myconfig.json

# Diff two engines
cfconfig diff from=lucee5 to=adobe2023
```

---

## Deployment Checklist

| Task | Command |
|------|---------|
| Set production profile | `server set profile=production` |
| Disable browser open | `server set openBrowser=false` |
| Set heap size | `server set jvm.heapSize=1024` |
| Block CF admin externally | `server set web.blockCFAdmin=external` |
| Block sensitive paths | `server set web.blockSensitivePaths=true` |
| Enable rewrites | `server set web.rewrites.enable=true` |
| Set ForgeBox token | `config set endpoints.forgebox.APIToken=...` |
| Install prod deps only | `box install --production` |
| Run tests in CI | `box testbox run` |