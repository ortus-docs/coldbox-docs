---
name: boxlang-application-descriptor
description: "Use this skill when designing or debugging Application.bx behavior: app discovery and nesting, multi-application isolation, lifecycle events, pseudo-constructor settings, session management, mappings/javaSettings resolution, and app-scoped schedulers/watchers via this.schedulers and this.watchers."
---

# BoxLang Application Descriptor

## Overview

`Application.bx` is BoxLang's application descriptor. It defines application-level settings and lifecycle callbacks, and it is the cornerstone for running multiple isolated applications in one BoxLang server/runtime.

Use this skill whenever the question involves:

- app bootstrapping and lifecycle events
- per-app configuration in `this.*`
- nested apps and scope isolation
- app-scoped schedulers and watchers
- differences between runtime config (`boxlang.json`) and app config (`Application.bx`)

## Core Model

- BoxLang searches upward from the executing template/script location for the nearest `Application.bx`.
- `Application.bx` is instantiated per request/execution context.
- The `application` scope is persistent for that app name and survives across requests until timeout/stop.
- Multiple unique app names create separate virtual applications in the same JVM.

## Multi-Application Isolation

Each unique `this.name` maps to a unique application memory space:

- isolated `application` scope
- isolated session tracking/cache namespace
- isolated app-level settings and listener behavior

This enables running multiple apps side-by-side (public site, admin site, API site) in a single server process.

## App Name Rules

```boxlang
class {
    this.name = "MyApp"
}
```

Best practices:

- Always set `this.name` explicitly.
- Use stable names across deployments unless you intentionally want a new app memory space.
- Avoid dynamic names unless you fully understand memory and lifecycle impact.

If no name is provided, runtime may auto-generate one from the descriptor path for class listeners, which is usually not what you want in production.

## Lifecycle Callbacks

Primary callbacks in `Application.bx`:

- `onApplicationStart()`
- `onApplicationEnd( applicationScope )`
- `onSessionStart()`
- `onSessionEnd( sessionScope, applicationScope )`
- `onRequestStart( targetPage )`
- `onRequest( targetPage )`
- `onRequestEnd()`
- `onError( exception, eventName )`
- `onMissingTemplate( targetPage )`
- `onAbort( targetPage )`
- `onClassRequest( className, method, args )`

```boxlang
class {

    this.name = "MyApp"
    this.sessionManagement = true

    function onApplicationStart() {
        application.bootedAt = now()
        return true
    }

    function onRequestStart( string targetPage ) {
        request.started = getTickCount()
        return true
    }

    function onError( any exception, string eventName ) {
        writeLog( text: "Error in #eventName#: #exception.message#", type: "Error" )
        return true
    }

}
```

## Pseudo-Constructor Settings

Common `this.*` settings in `Application.bx` include:

- identity and timing: `this.name`, `this.applicationTimeout`, `this.timezone`, `this.locale`
- sessions: `this.sessionManagement`, `this.sessionTimeout`, `this.sessionStorage`
- data and cache: `this.datasource`, `this.datasources`, `this.caches`
- loading and resolution: `this.mappings`, `this.classPaths`, `this.javaSettings`
- app async wiring: `this.schedulers`, `this.watchers`

## App-Scoped Schedulers and Watchers

`Application.bx` can wire async infrastructure per app:

```boxlang
class {

    this.name = "MyApp"

    this.schedulers = [
        "schedulers.DailyMaintenance",
        "schedulers.HourlyReports"
    ]

    this.watchers = {
        sourceWatcher : {
            paths : [ expandPath( "./src" ) ],
            listener : "app.listeners.HotReloadListener",
            recursive : true,
            debounce : 250,
            atomicWrites : true,
            errorThreshold : 10
        }
    }

}
```

For deeper async details, see:

- [scheduled-tasks](../scheduled-tasks/SKILL.md)
- [file-watchers](../file-watchers/SKILL.md)

## Runtime vs Application Configuration

- Use `boxlang.json` for runtime-wide defaults and infrastructure settings.
- Use `Application.bx` for app-specific behavior and overrides.

Rule of thumb:

- global and environment policy -> `boxlang.json`
- app identity, lifecycle, and request logic -> `Application.bx`

## Troubleshooting

- Wrong app behavior: verify which `Application.bx` is being discovered by directory traversal.
- Missing session hooks: verify `this.sessionManagement = true` and web runtime context.
- Recreated app state: check `this.applicationTimeout` and `applicationStop()` usage.
- Async registration not happening: verify `this.schedulers` / `this.watchers` values and listener/class paths.
- Mapping/classpath issues: verify paths are relative to the descriptor location and resolve as expected.