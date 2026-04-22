---
name: boxlang-runtime-desktop
description: "Use this skill when building BoxLang desktop applications with Electron and the BoxLang MiniServer, including process lifecycle wiring, miniserver.json-driven development control, runtime/Package.bx packaging, .boxlang-dev.json versus .boxlang.json behavior, default /app and /public mappings, and cross-platform packaging constraints."
---

# BoxLang Desktop Runtime (Electron + MiniServer)

## Overview

This skill applies to desktop applications that embed BoxLang using Electron and run a local BoxLang MiniServer process.

Core model:

- Electron owns desktop lifecycle (window, tray, menu, shortcuts).
- A local MiniServer process serves the BoxLang app.
- The BrowserWindow loads the local MiniServer URL.
- Frontend assets are served via Vite in development and built artifacts in production.

This pattern gives a desktop UX without replacing BoxLang with another backend stack.

---

## Architecture Baseline

Expected module boundaries:

- `app/electron/Main.js`: app bootstrap and module wiring.
- `app/electron/BoxLang.js`: MiniServer process manager (start/stop/restart/readiness).
- `app/electron/AppMenu.js`: application menu.
- `app/electron/TrayMenu.js`: tray behavior and status.
- `app/electron/Shortcuts.js`: global keyboard shortcuts.
- `runtime/Package.bx`: MiniServer runtime packager using `.bvmrc`.
- `miniserver.json`: local server runtime controls.
- `.boxlang-dev.json`: development BoxLang runtime defaults.
- `.boxlang.json`: production BoxLang runtime defaults.

Preserve modular boundaries in `app/electron/*` instead of collapsing logic into a single file.

---

## Packaging Model

Package the MiniServer runtime, not Java:

- Packaged runtime paths:
  - `runtime/bin`
  - `runtime/lib`
- Version source: `.bvmrc`
- Packager script: `runtime/Package.bx`
- Startup preference: packaged `runtime/bin/boxlang-miniserver` first, global `boxlang-miniserver` fallback.

Important runtime constraint:

- Java 21+ must exist on every machine running the desktop app.
- Only MiniServer binaries/libs are packaged, not a JRE/JDK.

---

## Configuration Responsibilities

### `miniserver.json` (development server control)

Use this as the primary local server control file for desktop development:

- host/port binding
- webRoot
- rewrites
- debug
- envFile
- serverHome

Example:

```json
{
    "port": 59700,
    "host": "127.0.0.1",
    "webRoot": "public",
    "serverHome": ".boxlang",
    "rewrites": true,
    "debug": false,
    "envFile": ".env"
}
```

### `.boxlang-dev.json` vs `.boxlang.json`

Use split runtime config for predictable behavior:

- `.boxlang-dev.json`: development-focused runtime behavior (`debugMode`, cache toggles, testing mappings).
- `.boxlang.json`: production defaults for packaged desktop execution.

Both should include baseline mappings so classes/templates resolve predictably:

```json
"/app": {
    "path": "${user-dir}/app",
    "external": false
},
"/public": "${user-dir}/public"
```

---

## Startup and Readiness Pattern

Recommended process flow in `BoxLang.js`:

1. Detect packaged runtime executable and fallback command.
2. Ensure execute permission on Unix-like systems.
3. Spawn MiniServer with `miniserver.json`.
4. Poll server URL readiness with timeout and retry interval.
5. On ready: load BrowserWindow URL.
6. On crash: apply controlled restart strategy with delay and user notification.

Checklist for robust lifecycle handling:

- [ ] startup timeout with clear error dialog
- [ ] readiness probe over HTTP before loading app URL
- [ ] restart suppression on intentional shutdown
- [ ] graceful stop on app quit/SIGINT/SIGTERM
- [ ] per-platform executable permission handling

---

## Build and Packaging Workflow

Common script set for this runtime style:

- `npm run dev`: Vite + Electron development loop.
- `npm run build`: build frontend assets to `public/includes/resources`.
- `npm run package:miniserver`: download/extract runtime from `.bvmrc`.
- `npm run package`: build assets and package desktop app.
- `npm run package:full`: package MiniServer, then package app.

Use `package:full` for distributable builds.

---

## BoxLang and Runtime Rules

- Use tag-based component syntax (`bx:http {}`, `bx:zip {}`), never `new bx:...`.
- For CLI scripts (such as runtime packaging), parse args with `CliGetArgs()`.
- Use `expandPath()` and check file/directory existence before destructive operations.
- Keep path construction cross-platform in Electron with `path.join()`.

---

## Troubleshooting Guide

### MiniServer fails to start

- Confirm `runtime/bin` and `runtime/lib` exist (run `npm run package:miniserver`).
- If using fallback mode, verify `boxlang-miniserver` is on `PATH`.
- Validate `miniserver.json` host/port/webRoot values.

### Permission denied on Unix-like hosts

- Repackage with force: `npm run package:miniserver:force`.
- Verify executable bit on `runtime/bin/boxlang-miniserver`.

### App runs but UI assets missing

- Run `npm run build` and confirm manifest exists in `public/includes/resources/.vite/manifest.json`.

### Runs on dev machine but fails on user machine

- Confirm Java 21+ is installed on target machine.
- Remember: MiniServer is bundled, JRE is not.

---

## Best Practices

- Keep desktop, runtime, and web concerns separated by module.
- Treat `miniserver.json` as developer-facing runtime control.
- Keep both `.boxlang-dev.json` and `.boxlang.json` under source control.
- Keep `/app` and `/public` mappings stable unless there is a deliberate architecture change.
- Preserve packaged runtime fallback logic unless explicitly removing it.