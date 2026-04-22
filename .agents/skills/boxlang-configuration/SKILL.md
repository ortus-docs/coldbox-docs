---
name: boxlang-configuration
description: "Use this skill when configuring BoxLang runtime settings via boxlang.json, setting environment variables for config overrides, configuring datasources, caches, executors, modules, logging, security, or schedulers — or when helping someone understand the BoxLang configuration system."
---

# BoxLang Configuration

## Overview

BoxLang's runtime is configured through `boxlang.json`. The file is auto-created in
the BoxLang home directory on first startup. All settings can be overridden via
environment variables or Java system properties without modifying the file.

When configuration questions involve app-specific behavior in `Application.bx`
(`this.name`, lifecycle callbacks, per-app schedulers/watchers, nested app
isolation), pair this with the
[`application-descriptor`](../application-descriptor/SKILL.md) skill.

---

## Config File Locations

| Runtime | Default Location |
|---------|----------------|
| OS / CLI / MiniServer | `~/.boxlang/config/boxlang.json` |
| AWS Lambda | `{lambdaRoot}/boxlang.json` |
| Google Cloud Functions | `{gcfRoot}/boxlang.json` |
| CommandBox | `~/.commandbox/servers/{home}/WEB-INF/boxlang/config/boxlang.json` |

Override the home directory at startup:

```bash
boxlang --home /path/to/custom-home
```

---

## Environment Variable Overrides

Any setting can be overridden via environment variable or JVM property using the
`BOXLANG_` prefix (snake_case) or `boxlang.` prefix (dot-notation):

```bash
# Enable debug mode
BOXLANG_DEBUGMODE=true

# Set log level for runtime logger
boxlang.logging.loggers.runtime.level=TRACE

# Set as JVM argument
java -Dboxlang.debugMode=true -jar boxlang.jar
```

JSON values are supported for complex overrides:

```bash
BOXLANG_DATASOURCES='{"mainDB":{"driver":"postgresql","host":"db.example.com"}}'
```

---

## Environment Variable Substitution

Use `${env.VAR:default}` inside `boxlang.json` to reference env vars:

```json5
{
    "datasources": {
        "mainDB": {
            "driver":   "postgresql",
            "host":     "${env.DB_HOST:localhost}",
            "port":     "${env.DB_PORT:5432}",
            "database": "${env.DB_NAME:myapp}",
            "username": "${env.DB_USER:app}",
            "password": "${env.DB_PASSWORD}"
        }
    }
}
```

Built-in path substitution variables:

| Variable | Resolves to |
|----------|-----------|
| `${boxlang-home}` | BoxLang home directory |
| `${user-home}` | OS user home directory |
| `${user-dir}` | Current working directory |
| `${java-temp}` | Java temp directory |

---

## Core Directives

```json5
{
    // Compiled class output directory
    "classGenerationDirectory": "${boxlang-home}/classes",

    // Compiler backend: "asm" (default, best performance) or "java"
    "compiler": "asm",

    // Store compiled classes on disk (recommended: true for production)
    "storeClassFilesOnDisk": true,

    // Never re-check class files (recommended: true for production)
    "trustedCache": false,

    // Cache class resolver lookups (recommended: true)
    "classResolverCache": true,

    // Clear class files on startup (use only for debugging)
    "clearClassFilesOnStartup": false,

    // Global class paths (.bx file discovery)
    "classPaths": [
        "${boxlang-home}/global/classes"
    ],

    // Custom component directories
    "customComponentsDirectory": [
        "${boxlang-home}/global/components"
    ],

    // Default datasource name
    "defaultDatasource": "",

    // Max completed threads tracked per request
    "maxTrackedCompletedThreads": 1000,

    // Enable debug output in responses
    "debugMode": false
}
```

---

## Datasources

```json5
{
    "datasources": {
        "mainDB": {
            "driver":   "postgresql",
            "host":     "${env.DB_HOST:localhost}",
            "port":     5432,
            "database": "myapp",
            "username": "${env.DB_USER:app}",
            "password": "${env.DB_PASSWORD}"
        },
        "legacyMySQL": {
            "driver":   "mysql",
            "host":     "db2.internal",
            "port":     3306,
            "database": "legacy",
            "username": "${env.MYSQL_USER}",
            "password": "${env.MYSQL_PASS}",
            // Connection pool settings
            "connectionTimeout":   30,
            "maximumPoolSize":     10,
            "minimumIdle":         2
        }
    }
}
```

Supported drivers: `postgresql`, `mysql`, `mssql`, `oracle`, `derby`, `h2`, `sqlite`.

---

## Caches

```json5
{
    "caches": {
        // Default cache (used when no cache name specified)
        "default": {
            "provider": "BoxCacheProvider",
            "properties": {
                "maxObjects":       1000,
                "defaultTimeout":   60,
                "defaultLastAccessTimeout": 0,
                "objectStore":      "ConcurrentStore"
            }
        },
        // Named cache for templates
        "templates": {
            "provider": "BoxCacheProvider",
            "properties": {
                "maxObjects": 500,
                "defaultTimeout": 120
            }
        }
    }
}
```

---

## Executors

```json5
{
    "executors": {
        // Default executor for runAsync() — virtual threads
        "boxlang-tasks": {
            "type": "virtual",
            "coreThreads": 20
        },
        // CPU-bound work pool
        "cpu-work": {
            "type": "fixed",
            "coreThreads": 8
        },
        // Elastic pool for bursty I/O
        "io-tasks": {
            "type": "cached"
        },
        // Scheduler pool
        "scheduler": {
            "type": "scheduled",
            "coreThreads": 5
        }
    }
}
```

Executor types: `virtual` (default, Project Loom), `fixed`, `cached`, `scheduled`, `work_stealing`.

---

## Logging

```json5
{
    "logging": {
        "logsDirectory": "${boxlang-home}/logs",
        "level": "WARN",   // Global default: TRACE, DEBUG, INFO, WARN, ERROR
        "loggers": {
            "runtime":    { "level": "INFO" },
            "scheduler":  { "level": "INFO", "async": true },
            "datasource": { "level": "WARN" },
            "cache":      { "level": "WARN" },
            "modules":    { "level": "INFO" }
        }
    }
}
```

---

## Security

```json5
{
    "security": {
        // Regex patterns for blocked Java class imports
        "disallowedImports": [],
        // Blocked BIF names
        "disallowedBifs": [],
        // Blocked component names
        "disallowedComponents": [],
        // Whether Java system props/env are in server.system scope
        "populateServerSystemScope": true,
        // Explicit upload extension whitelist (overrides disallowed list)
        "allowedFileOperationExtensions": [],
        // Blocked file upload/move extensions
        "disallowedFileOperationExtensions": []
    }
}
```

---

## Modules

Configure modules loaded at startup and module-specific settings:

```json5
{
    "modules": {
        // Modules to load on startup (in addition to auto-discovered modules)
        "load": [ "bx-compat-cfml", "bx-orm" ],
        // Modules to skip loading even if present
        "exclude": [],
        // Per-module settings
        "settings": {
            "bx-orm": {
                "autoManageSession": true,
                "dialect":           "PostgreSQLDialect"
            }
        }
    }
}
```

---

## Scheduler

```json5
{
    "scheduler": {
        // Default task timeout (0 = no timeout)
        "defaultTimeout": 0,
        // How long to wait for scheduled tasks to complete on shutdown
        "shutdownTimeout": 30,
        // Executor to use for scheduled tasks
        "executor": "scheduler"
    }
}
```

---

## Watcher

Configure the built-in `WatcherService` for filesystem monitoring. Watchers react to file/directory changes and can trigger hot-reload, asset builds, or custom automation.

```json5
{
    "watcher": {
        // Recurse into subdirectories by default
        "recursive": true,
        // Hold events until no new event arrives within this window (ms); 0 = off
        "debounce": 300,
        // Emit at most one event per window and drop the rest (ms); 0 = off
        "throttle": 0,
        // Suppress noisy intermediate events from atomic save patterns (temp + rename)
        "atomicWrites": true,
        // Startup delay before watchers begin processing events (ms)
        "delay": 0,
        // Auto-stop watcher after this many consecutive listener errors (0 = disabled)
        "errorThreshold": 10,
        // Named watcher definitions auto-started at runtime startup
        "definitions": {
            "hot-reload": {
                "paths":    "${user-dir}/src",
                "listener": "app.listeners.HotReloadListener"
            },
            "assets": {
                "paths":     [ "${user-dir}/resources/css", "${user-dir}/resources/js" ],
                "recursive": false,
                "throttle":  500,
                "listener":  "app.listeners.AssetPipelineListener"
            }
        }
    }
}
```

### Watcher Definition Properties

| Property | Required | Description |
|----------|----------|-------------|
| `paths` | Yes | Directory path or array of paths to watch |
| `listener` | Yes | BoxLang class path with listener behavior |
| `recursive` | No | Override global recursive setting |
| `debounce` | No | Per-watcher debounce override (ms) |
| `throttle` | No | Per-watcher throttle override (ms) |
| `atomicWrites` | No | Per-watcher atomic write filtering override |
| `delay` | No | Per-watcher startup delay override (ms) |
| `errorThreshold` | No | Per-watcher error threshold override |

> For closures and struct-based listeners, create watchers programmatically with `watcherNew()` at runtime instead of in `boxlang.json`.

### Runtime Watcher BIFs

`watcherNew()`, `watcherStart()`, `watcherStop()`, `watcherRestart()`, `watcherList()`, `watcherGet()`, `watcherExists()`, `watcherShutdown()`, `watcherStopAll()`, `watcherShutdownAll()`

---

## Experimental

Feature flags for in-progress BoxLang capabilities. These settings may change or be removed.

```json5
{
    "experimental": {
        // Compiler backend: "asm" (default, direct bytecode) or "java" (transpile to Java first)
        "compiler": "asm",
        // Capture AST JSON to /grapher/data on each parse (for tooling/debugging only)
        "ASTCapture": false
    }
}
```

| Flag | Default | Description |
|------|---------|-------------|
| `compiler` | `"asm"` | `"asm"` compiles directly to bytecode (production default); `"java"` transpiles to Java source first |
| `ASTCapture` | `false` | Writes AST JSON to `/grapher/data` on every parse — for tooling and debugging only, never production |

---

## Application-Level Configuration (`Application.bx`)

Application-level settings override the runtime config for a specific app:

```boxlang
class {
    // Application identity
    this.name             = "MyApp"
    this.sessionManagement = true
    this.sessionTimeout   = createTimeSpan( 0, 2, 0, 0 )

    // Datasource
    this.datasource = "mainDB"

    // Per-application datasource definition
    this.datasources = {
        localDB: {
            driver:   "h2",
            database: expandPath( "/db/local.h2" )
        }
    }

    // Java library paths
    this.javaSettings = {
        loadPaths: [ expandPath( "/lib/" ) ]
    }

    // Cache mappings
    this.caches = {
        objects: { provider: "BoxCacheProvider" }
    }

    // Directory mappings
    this.mappings = {
        "/models":   expandPath( "/app/models/" ),
        "/services": expandPath( "/app/services/" )
    }
}
```

---

## Production Configuration Checklist

- [ ] `trustedCache: true` — no disk checks on every request
- [ ] `classResolverCache: true` — cached class resolution
- [ ] `debugMode: false` — no debug output to responses
- [ ] `logging.level: "WARN"` — suppress verbose logs
- [ ] Secrets via `${env.VAR}` — never hardcoded in `boxlang.json`
- [ ] `classGenerationDirectory` on a fast disk (SSD/tmpfs)
- [ ] Connection pool sizes tuned for expected concurrency
- [ ] `security.populateServerSystemScope: false` if system env not needed