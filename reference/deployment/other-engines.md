---
description: Deploy ColdBox on Adobe ColdFusion and Lucee — WAR files, CommandBox servers, and engine-specific notes.
icon: server
---

# Deploying on Adobe ColdFusion & Lucee

ColdBox fully supports both CFML engines. **BoxLang is the preferred runtime** — see [BoxLang Runtimes](boxlang-runtimes.md) — but existing Adobe and Lucee estates run ColdBox 8 without changes.

## Adobe ColdFusion

Requires **ColdFusion 2023 or newer** (CF 2021 support was dropped in ColdBox 8).

### CommandBox (recommended)

```bash
box server start cfengine=adobe@2023
```

CommandBox manages the engine, JVM, and web server for you — keep `server.json` in source control so production mirrors development.

### WAR / J2EE deployment

Install ColdBox into your application (`box install coldbox`) and package the app directory as a WAR through your standard Adobe CF admin or build pipeline. Ensure the engine mappings resolve your `coldbox` folder or configure a per-application mapping.

### Adobe notes

- Keep the engine fully patched — several ColdBox guards exist for engine quirks fixed in later updates
- Java settings (heap, GC) matter: tune in the CF admin or `jvm.config`
- Session scope behavior differs subtly from BoxLang — enable robust session config in the CF admin

## Lucee

Requires **Lucee 5+**; Lucee 6/7 recommended.

### CommandBox (recommended)

```bash
box server start cfengine=lucee@6
```

### WAR deployment

Drop the app into a Lucee web context or build a WAR. Lucee's per-web-context mappings make the `coldbox` mapping straightforward — see your `Application.cfc`/`this.mappings` configuration.

### Lucee notes

- Null support behavior differs from BoxLang — test thoroughly if enabling full null support
- The Lucee admin's datasource and cache definitions can back CacheBox providers directly

## Shared Production Checklist

Whatever the engine:

- [ ] `debugMode = false` in production
- [ ] `reinitPassword` set and secret
- [ ] Secrets in `.env`, never in source control
- [ ] Handler/event/view caching enabled
- [ ] Real CacheBox caches configured
- [ ] Health route wired to your monitor
- [ ] Scheduled tasks use `onOneServer()` on multi-instance deployments
- [ ] HTTPS enforced at the edge

Full details: [Deployment Overview](README.md).
