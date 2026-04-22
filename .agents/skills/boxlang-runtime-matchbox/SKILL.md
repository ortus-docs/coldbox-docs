---
name: boxlang-runtime-matchbox
description: "Use this skill when using MatchBox, the Rust-based native implementation of BoxLang that requires no JVM, including installation, understanding the available compilation targets (native, wasm, js, esp32), the philosophy and tradeoffs vs JVM BoxLang, and choosing the right target for CLI tools, edge deployments, browser WASM, and embedded devices."
---

# MatchBox — BoxLang Without the JVM

## Overview

**MatchBox** is a native Rust implementation of the BoxLang runtime. It compiles BoxLang source code to a custom bytecode and executes it without any JVM required. MatchBox is ideal for environments where the JVM is unavailable, cold-start time matters, or binary size must be minimal.

> MatchBox code is a **strict subset** of JVM BoxLang. Code written for MatchBox runs on JVM BoxLang, but not vice versa — some JVM-specific APIs are unavailable in MatchBox.

---

## Installation

```bash
# Install via the official install script (macOS, Linux)
curl -sSL https://raw.githubusercontent.com/ortus-boxlang/matchbox/master/install/install.sh | bash

# Verify
matchbox --version
```

---

## Architecture

| Component | Technology | Description |
|-----------|-----------|-------------|
| Parser | Pest PEG (Rust) | Parses BoxLang source to AST |
| Compiler | Rust | Compiles AST to MatchBox bytecode |
| VM | Stack-based bytecode VM (Rust) | Executes bytecode at runtime |
| Targets | `native`, `wasm`, `js`, `esp32` | Output deployment formats |

---

## Compilation Targets

| Target | Command | Output | Use Case |
|--------|---------|--------|----------|
| `native` | `matchbox --target native app.bxs` | Executable binary | CLI tools, microservices |
| `wasm` | `matchbox --target wasm app.bxs` | `.wasm` file | Edge/FaaS, Wasmtime, containers |
| `js` | `matchbox --target js app.bxs` | `.js` + `.wasm` ES module | Web browsers, Node.js |
| `esp32` | `matchbox --target esp32 --chip esp32s3 app.bxs` | Firmware | Microcontrollers |

---

## Running Scripts Directly

Without a `--target` flag, MatchBox interprets and runs BoxLang immediately:

```bash
# Run a script directly (JVM-like execution, no output file)
matchbox hello.bxs

# With arguments
matchbox process.bxs --input=data.json --output=result.json
```

---

## JVM BoxLang vs. MatchBox

| Aspect | JVM BoxLang | MatchBox |
|--------|-------------|----------|
| Runtime | JVM (Java 21+) | No JVM — pure Rust |
| Cold start | 300ms–2s | < 10ms |
| Binary size | ~50MB JAR | ~500KB executable |
| Full BoxLang API | ✅ All BIFs | ✅ Core subset |
| Java interop | ✅ Full | ❌ None |
| CFML compat | ✅ (`bx-compat-cfml`) | ❌ |
| Browser (WASM/JS) | ❌ | ✅ |
| Embedded (ESP32) | ❌ | ✅ |
| Strict subset | Use either | Write for MatchBox, runs on JVM too |

---

## What You Can Build

- **CLI tools** — Single-file executables with no runtime dependency
- **Edge/FaaS functions** — Cold-start-sensitive serverless deployments (Fastly, Cloudflare Workers)
- **Browser apps** — Compile BoxLang logic to WebAssembly or ES modules
- **Microservices** — Tiny containers with just a `.wasm` binary + scratch image
- **IoT/embedded** — BoxLang on ESP32 microcontrollers via FreeRTOS

---

## One-Way Compatibility

Code written for MatchBox is a strict subset and runs on JVM BoxLang unchanged. This lets you develop locally with JVM BoxLang tooling and deploy anywhere:

```bash
# Test with JVM BoxLang locally
boxlang app.bxs

# Deploy as native binary
matchbox --target native app.bxs

# Deploy as WASM
matchbox --target wasm app.bxs
```

---

## Related Skills

- [`compiled-native-binaries`](../compiled-native-binaries/SKILL.md) — `--target native` single binaries + Native Fusion Rust interop
- [`wasm-container`](../wasm-container/SKILL.md) — `--target wasm` for server-side WASM and OCI containers
- [`wasm-in-the-browser`](../wasm-in-the-browser/SKILL.md) — `--target js` / `--target wasm` for browsers and Node.js
- [`esp32`](../esp32/SKILL.md) — `--target esp32` for microcontroller deployments

---

## Checklist

- [ ] Install via the official `install.sh` script
- [ ] Write BoxLang code that avoids JVM-specific APIs (Java interop, full CFML compat)
- [ ] Use `matchbox app.bxs` to test locally before compiling to a target
- [ ] Choose the right target for your deployment: `native` > `wasm` > `js` > `esp32`
- [ ] Remember: MatchBox code runs on JVM BoxLang, but not vice versa