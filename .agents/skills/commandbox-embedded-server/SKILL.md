---
name: commandbox-embedded-server
description: "Use this skill for the CommandBox embedded server: starting and stopping servers, server.json configuration, JVM args, SSL/TLS setup, URL rewrites, server rules/security, multi-site hosting, server profiles (production/development), basic authentication, bindings, custom error pages, aliases, gzip compression, web roots, HTTPS redirect, and starting as an OS service."
---

# CommandBox Embedded Server

## Overview

CommandBox's embedded server uses [Undertow](https://undertow.io/) to run CFML/BoxLang applications without requiring Apache, IIS, or Nginx. Each server is independently configured via `server.json`.

---

## Quick Start

```bash
# Start server in current directory (auto-selects port)
server start

# Start with specific options
server start port=8080 host=localhost

# Start in foreground (no browser)
server start --noOpenBrowser

# Stop server
server stop

# Stop by name
server stop name=myApp

# Restart
server restart

# List all servers
server list
server list --verbose

# Show server info
server info
server info --json
```

---

## `server.json` Configuration

Place `server.json` in your project root. Settings from `server start` command-line args are automatically saved here unless `--noSaveSettings` is passed.

### Minimal Configuration

```json
{
    "name": "myApp",
    "openBrowser": false
}
```

### Full Reference

```json
{
    "name": "myApp",
    "openBrowser": false,
    "openBrowserURL": "http://localhost/admin/",
    "startTimeout": 240,
    "debug": false,
    "trace": false,
    "console": false,
    "profile": "production",
    "trayEnable": true,
    "env": {
        "MY_VAR": "my-value",
        "DB_HOST": "localhost"
    },
    "app": {
        "cfengine": "lucee@5",
        "serverHomeDirectory": ".engine",
        "webXML": "",
        "WARPath": ""
    },
    "jvm": {
        "heapSize": "512m",
        "minHeapSize": "256m",
        "args": [
            "-XX:+UseG1GC",
            "-XX:-CreateMinidumpOnCrash",
            "--add-opens=java.base/java.net=ALL-UNNAMED"
        ],
        "javaHome": "/path/to/java",
        "javaVersion": "openjdk21_jdk"
    },
    "web": {
        "webroot": "www",
        "host": "127.0.0.1",
        "directoryBrowsing": false,
        "accessLogEnable": true,
        "maxRequests": 30,
        "gzipEnable": true,
        "gzipPredicate": "regex('(.*).css') and request-larger-than(500)",
        "welcomeFiles": "index.cfm,index.bxm,index.html",
        "aliases": {
            "/assets": "../shared-static",
            "/js": "C:/static/js"
        },
        "errorPages": {
            "404": "/errors/404.html",
            "500": "/errors/500.html",
            "default": "/errors/default.html"
        },
        "bindings": {
            "HTTP": {
                "listen": "8080"
            },
            "SSL": {
                "listen": 8443,
                "certFile": "/certs/server.crt",
                "keyFile": "/certs/server.key",
                "keyPass": ""
            },
            "AJP": {
                "listen": 8009
            }
        },
        "rewrites": {
            "enable": true,
            "logEnable": false,
            "statusPath": "/rewriteStatus"
        },
        "basicAuth": {
            "enable": false,
            "users": {
                "admin": "secret123"
            }
        },
        "blockCFAdmin": "external",
        "blockSensitivePaths": true,
        "blockFlashRemoting": true,
        "rules": [
            "path-suffix(/box.json) -> set-error(404)",
            "path-prefix(/.env) -> set-error(404)",
            "path-prefix(/admin/) -> ip-access-control(192.168.0.* allow)",
            "path(/sitemap.xml) -> rewrite(/sitemap.cfm)"
        ]
    }
}
```

---

## Server Profiles

| Profile | directoryBrowsing | blockCFAdmin | blockSensitivePaths |
|---------|-------------------|--------------|---------------------|
| `production` | false | external | true |
| `development` | true | false | true |
| `none` | false | false | false |

```bash
# Set profile in server.json
server set profile=production

# Default profile rules:
# - localhost → development
# - env var "environment" → matched to profile name
# - all other → production (secure by default)
```

---

## JVM Configuration

```bash
# Set heap size
server set jvm.heapSize=1024
server set jvm.heapSize=2G
server set jvm.minHeapSize=512

# Add JVM args
server set jvm.args="-XX:+UseG1GC"

# Array of args (no quoting/escaping needed)
server set jvm.args=["-XX:+UseG1GC","-XX:-CreateMinidumpOnCrash"]
server set jvm.args=["--add-opens=java.base/java.net=ALL-UNNAMED"] --append

# Use specific Java version
server set jvm.javaVersion=openjdk21_jdk

# List/install Java versions
java list
java install openjdk21_jdk
```

---

## SSL/TLS Configuration

```bash
# Generate self-signed cert for development
server set web.bindings.SSL.listen=8443
server set web.bindings.SSL.certFile=/path/cert.pem
server set web.bindings.SSL.keyFile=/path/key.pem

# Let's Encrypt (via commandbox-acme module)
install commandbox-acme
server set web.bindings.SSL.listen=443
```

### HTTPS Redirect & HSTS

```json
{
    "web": {
        "bindings": {
            "HTTP": { "listen": 80 },
            "SSL": { "listen": 443, "certFile": "...", "keyFile": "..." }
        },
        "HSTS": {
            "enable": true,
            "maxAge": 31536000,
            "includeSubdomains": true,
            "preload": true
        }
    }
}
```

---

## URL Rewrites

CommandBox 6+ uses **Server Rules** (Undertow predicate language) for rewrites:

```bash
# Enable default rewrites (maps index.cfm/bxm)
server set web.rewrites.enable=true
```

### Server Rules for Rewrites

```json
{
    "web": {
        "rules": [
            "path(/sitemap.xml) -> rewrite(/sitemap.cfm)",
            "path-prefix(/api/) -> rewrite(/api/index.cfm)",
            "regex('/blog/([0-9]+)') -> rewrite('/blog/post.cfm?id=${1}')"
        ]
    }
}
```

### Tuckey Legacy Rewrites (CommandBox < 6)

```json
{
    "web": {
        "rewrites": {
            "enable": true,
            "config": "/path/to/urlrewrite.xml"
        }
    }
}
```

---

## Server Rules (Security & Routing)

```json
{
    "web": {
        "rules": [
            "path-suffix(/box.json) -> set-error(404)",
            "path-prefix(/.env) -> set-error(404)",
            "path-prefix(/.git) -> set-error(404)",
            "path-suffix(.cfm) -> rewrite(${1}/index.cfm)",
            "path-prefix(/admin/) -> ip-access-control(192.168.0.* allow)",
            "method(OPTIONS) -> set-error(200)"
        ]
    }
}
```

```bash
# Set rules from CLI
server set web.rules=["path-suffix(/box.json) -> set-error(404)"]
server set web.rules=["path-prefix(/.env) -> set-error(404)"] --append
```

---

## Multi-Site Support (CommandBox 6+)

Host multiple web roots in a single server process:

```json
{
    "web": {
        "bindings": {
            "HTTP": { "listen": 80 },
            "SSL": { "listen": 443 }
        }
    },
    "sites": {
        "site1": {
            "hostAlias": "site1.com",
            "webroot": "/var/www/site1",
            "profile": "production",
            "rewrites": { "enable": true }
        },
        "site2": {
            "hostAlias": "site2.com",
            "webroot": "/var/www/site2",
            "profile": "development"
        }
    }
}
```

Or use `.site.json` files per directory. > **Note**: For >2 sites in production, a [CommandBox Pro](https://www.ortussolutions.com/products/commandbox-pro) license is requested.

---

## Basic Authentication

```json
{
    "web": {
        "basicAuth": {
            "enable": true,
            "users": {
                "admin": "p@ssw0rd",
                "readonly": "readpass"
            }
        }
    }
}
```

```bash
server set web.basicAuth.enable=true
server set web.basicAuth.users.admin=secretpass
```

---

## Server Bindings

```bash
# HTTP on specific port
server set web.bindings.HTTP.listen=8080

# Bind to specific host
server set web.host=0.0.0.0

# SSL
server set web.bindings.SSL.listen=8443

# AJP (for Apache/Nginx proxy)
server set web.bindings.AJP.listen=8009
```

---

## Custom Error Pages

```json
{
    "web": {
        "errorPages": {
            "404": "/errors/notfound.html",
            "500": "/errors/servererror.html",
            "default": "/errors/default.html"
        }
    }
}
```

---

## Web Aliases

```json
{
    "web": {
        "aliases": {
            "/shared-assets": "../shared-assets",
            "/uploads": "/var/uploads"
        }
    }
}
```

---

## Starting as an OS Service

```bash
# Install as Windows service
server start --serviceInstall

# Or use NSSM (Non-Sucking Service Manager) on Windows / launchd on Mac
# CommandBox supports server scripts for lifecycle hooks
```

### Server Scripts (`server.json`)

```json
{
    "scripts": {
        "onServerStart": "echo 'Server starting...'",
        "onServerStop": "echo 'Server stopped'",
        "onServerInstall": "migrate up",
        "onServerInitialInstall": "seed run"
    }
}
```

---

## Managing Multiple Servers

```bash
# Start named server from any directory
server start name=myApp webroot=/path/to/webroot

# Stop by name
server stop name=myApp

# List all known servers
server list

# Forget a stopped server (removes from registry)
server forget name=myApp

# Show server logs
server log name=myApp
server log name=myApp --follow

# Open server home directory
server open name=myApp serverHomeDirectory

# Show server info as JSON
server info name=myApp --json
```

---

## Performance Tuning

```json
{
    "web": {
        "maxRequests": 50,
        "gzipEnable": true
    },
    "jvm": {
        "heapSize": "2G",
        "minHeapSize": "512m",
        "args": ["-XX:+UseG1GC", "-XX:MaxGCPauseMillis=200"]
    }
}
```

---

## Warmup URLs

Pre-warm your application on server start:

```json
{
    "web": {
        "warmup": {
            "enable": true,
            "URIs": ["/", "/api/health", "/admin/init"]
        }
    }
}
```

---

## Global Server Defaults

Set defaults for all servers via config:

```bash
config set server.defaults.jvm.heapSize=1024
config set server.defaults.web.directoryBrowsing=false
config set server.defaults.openBrowser=false
config set server.defaults.profile=production
```