---
description: Deploying ColdBox to production — best practices, production checklist, and engine guidance.
icon: rocket
---

# Deployment Overview & Best Practices

ColdBox applications deploy on three engines — **BoxLang (recommended)**, Adobe ColdFusion, and Lucee. This page covers the engine-agnostic essentials; per-engine specifics live in [BoxLang Runtimes](boxlang-runtimes.md) and [Adobe & Lucee](other-engines.md).

## Server Requirements

- A JVM (BoxLang and Lucee 6+ run on modern JDKs; Adobe CF 2023 bundles its own)
- [CommandBox](https://commandbox.com) for server orchestration (recommended for all engines)
- Your application's dependencies installed: `box install`

## Production Configuration

### Turn debug output off

In production, `debugMode` must be `false` in your `ColdBox` class — debug mode exposes framework internals in error output. Environment detection can do this automatically:

```javascript
// config/ColdBox.bx
environments : {
    development : "localhost,^dev\.",
    production  : ".*"
}

function development(){
    coldbox.debugMode = true
}
```

### Protect reinitialization

Never expose `?fwreinit=1` publicly. Set a strong `reinitPassword` and reinit through it:

```javascript
coldbox.reinitPassword = getSystemSetting( "CB_REINIT_PASSWORD" )
```

### Externalize secrets

Keep credentials out of source control with a `.env` file and `getSystemSetting()` — see [System Settings](../system-settings.md).

### Enable caching

- `handlerCaching`, `eventCaching`, and `viewCaching` should be `true` in production
- Configure real [CacheBox](../configuration-directives/cachebox.md) caches — the default in-memory cache doesn't scale across instances

## Health Checks

Give your load balancer / uptime monitor a dedicated route:

```javascript
// config/Router.bx
route( "/health" ).toResponse( () => { "status" : "ok" } )
```

## Scheduled Tasks & Workers

- [Scheduled tasks](../../digging-deeper/scheduled-tasks.md) run in-process — use server fixation (`onOneServer()`) on multi-instance deployments so tasks don't duplicate
- [cbq](../../ecosystem/queues.md) workers are long-running processes — supervise them (systemd, Docker restart policy, platform process manager) and restart on every deploy

## See Also

- [Engine Support & Differences](../engine-support.md)
- [Configuration Directives](../configuration-directives/README.md)
