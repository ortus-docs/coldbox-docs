---
name: boxlang-runtime-desktop-electron
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

## Electron Forge

This project uses **Electron Forge** as the packaging and distribution tool (`forge.config.cjs`).

- Docs / MCP reference: <https://www.electronforge.io/~gitbook/mcp>
- Config file must be **CommonJS** (`forge.config.cjs`) — Forge does not support ESM config files.
- Invoked via `electron-forge make` (wrapped in the `package` / `package:mac` / `package:win` / `package:linux` npm scripts).

### Critical constraint — `asar` must always be `false`

```js
packagerConfig: {
    asar: false,
    ...
}
```

BoxLang.js spawns `runtime/bin/boxlang-miniserver` as a real OS process. If `asar: true`, Forge archives the app directory into a single `.asar` file and the spawned path no longer exists on the filesystem, breaking MiniServer startup entirely.

### Icon resolution

Forge resolves icons from the base name in `packagerConfig.icon`:

```js
icon: "./public/includes/icon",
```

It then appends the platform-appropriate extension automatically:

| Platform | Extension | Source |
|----------|-----------|--------|
| macOS    | `.icns`   | `public/includes/icon.icns` (from iconset via `iconutil`) |
| Windows  | `.ico`    | `public/includes/icon.ico` (generated by `npm run generate:icons`) |
| Linux    | `.png`    | `public/includes/icon.iconset/icon.png` |

All three files must exist at package time. Missing any one causes a fatal Forge error on that platform.

### Makers

| Platform | Maker | Output |
|----------|-------|--------|
| macOS    | `@electron-forge/maker-dmg`, `@electron-forge/maker-zip` | `.dmg`, `.zip` |
| Windows  | `@electron-forge/maker-squirrel`, `@electron-forge/maker-zip` | `.exe` (Squirrel), `.zip` |
| Linux    | `@electron-forge/maker-deb`, `@electron-forge/maker-rpm` | `.deb`, `.rpm` |

Windows installer uses **Squirrel**, not NSIS.

### postMake hook

The config includes a `postMake` hook that copies unsigned-build helper scripts (`mac-open.sh`, `win-unblock.ps1`, `UNSIGNED-BUILD.md`) into every ZIP artifact so end users have bypass instructions without hunting for docs.

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

## CI / GitHub Actions Constraints

### Node.js Version for Windows Builds

Pin the Windows runner to **Node 20 LTS**. `cross-zip` (a transitive dependency of `@electron-forge/maker-zip`) calls `fs.rmdir(path, { recursive: true })`, which was removed in Node.js 22+. Running Node 25 on `windows-latest` causes a hard failure during the Squirrel make step:

```
TypeError [ERR_INVALID_ARG_VALUE]: The property 'options.recursive' is no longer supported. Received true
```

Workaround in `.github/workflows/build.yml`:

```yaml
- name: Set up Node.js
  uses: actions/setup-node@v6
  with:
    # Pin to Node 20 LTS — cross-zip uses fs.rmdir({recursive:true}) removed in Node 22+
    node-version: "20"
    cache: "npm"
```

Revisit when `cross-zip` or `@electron-forge/maker-zip` ships a patched release.

### Windows `.ico` Icon Requirement

The Squirrel installer (`@electron-forge/maker-squirrel`) requires a real `.ico` file on Windows. Forge auto-resolves icons from the base name configured in `packagerConfig.icon`. A missing `.ico` produces:

```
Fatal error: Unable to set icon
```

The `scripts/generate-icons.js` script generates `public/includes/icon.ico` from the PNG iconset. Run it in CI **before** packaging:

```yaml
- name: Generate Windows icon
  run: npm run generate:icons

- name: Package Electron app (Windows)
  run: npm run package:win
```

Alternatively, commit `public/includes/icon.ico` to the repository to skip the generation step in CI entirely (recommended for faster, more predictable builds).

---

## Agent Instruction Conventions

For repositories using this starter, adopt the two-file instruction pattern for maximum agent compatibility:

- **`AGENTS.md`** at the repository root: canonical instructions, full project rules. Read by Claude, Copilot, and most coding agents.
- **`.github/copilot-instructions.md`**: a compact compatibility bridge that points to `AGENTS.md` and repeats critical safety guardrails.

Template for `.github/copilot-instructions.md`:

```markdown
# Project — Copilot Bridge Instructions

Use AGENTS.md at repository root as the canonical instruction source.

## Canonical Source

- Primary instructions: AGENTS.md
- If any conflict exists, AGENTS.md wins.

## Critical Guardrails

- Keep modular boundaries in app/electron/* — do not collapse into a single file.
- Preserve process lifecycle safety in MiniServer startup and shutdown paths.
- Preserve cross-platform behavior (macOS, Windows, Linux).
- Do not remove packaged-runtime-first fallback to global boxlang-miniserver.
- Never enable asar in forge.config.cjs — MiniServer spawning requires real filesystem paths.
```

---

## Best Practices

- Keep desktop, runtime, and web concerns separated by module.
- Treat `miniserver.json` as developer-facing runtime control.
- Keep both `.boxlang-dev.json` and `.boxlang.json` under source control.
- Keep `/app` and `/public` mappings stable unless there is a deliberate architecture change.
- Preserve packaged runtime fallback logic unless explicitly removing it.
- Commit `public/includes/icon.ico` to avoid Windows icon generation in CI.
- Pin Windows CI runner to Node 20 LTS until `cross-zip` is updated for Node 22+.