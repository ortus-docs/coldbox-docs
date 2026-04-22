---
name: boxlang-runtime-wasm-container
description: "Use this skill when compiling BoxLang applications to server-side WebAssembly (WASM) using MatchBox's --target wasm flag, running WASM with Wasmtime or WasmEdge, building minimal OCI containers from WASM binaries, and deploying to edge platforms like Fastly Compute or Cloudflare Workers (WASI)."
---

# BoxLang WASM Containers (Server-Side)

## Overview

MatchBox can compile BoxLang source to **WebAssembly (WASM)** for server-side execution. WASM containers run without a JVM and are sandboxed by design, making them ideal for edge deployments, FaaS, and microservices in minimal OCI containers.

> This skill covers **server-side WASM** (Wasmtime, WasmEdge, OCI containers). For browser/Node.js WASM, see the [`wasm-in-the-browser`](../wasm-in-the-browser/SKILL.md) skill.

---

## Compiling to WASM

```bash
# Compile to .wasm
matchbox --target wasm my_service.bxs
# Output: my_service.wasm
```

---

## Running with Wasmtime

```bash
# Install Wasmtime
curl https://wasmtime.dev/install.sh -sSf | bash

# Run the WASM module
wasmtime my_service.wasm

# With filesystem access (needed for file I/O)
wasmtime --dir=. my_service.wasm

# With network access (experimental WASI sockets)
wasmtime --wasi-modules=experimental-wasi-sockets my_service.wasm

# Pass CLI arguments
wasmtime my_service.wasm -- --input=data.json --debug
```

---

## Running with WasmEdge

```bash
# Install WasmEdge
curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash

# Run
wasmedge my_service.wasm

# With WASI filesystem
wasmedge --dir=. my_service.wasm
```

---

## OCI Container (Minimal Docker Image)

Build a Docker image containing only the WASM binary — results in an extremely small image:

```dockerfile
# Multi-stage: compile with MatchBox, package as WASM container
FROM ghcr.io/ortus-boxlang/matchbox:latest AS builder

WORKDIR /build
COPY my_service.bxs .
RUN matchbox --target wasm my_service.bxs

# Final image: scratch + WASM binary only
FROM scratch
COPY --from=builder /build/my_service.wasm /app.wasm
ENTRYPOINT ["/app.wasm"]
```

Run with a WASM-capable container runtime (e.g., containerd + WasmEdge shim):

```bash
docker run --runtime=io.containerd.wasmedge.v1 myimage:latest
```

---

## WASI Capabilities

WASM modules are sandboxed by default. Grant capabilities explicitly:

| Wasmtime Flag | Capability |
|---------------|-----------|
| `--dir=.` | Filesystem access (current dir) |
| `--dir=/data` | Filesystem access (specific dir) |
| `--env KEY=VALUE` | Environment variables |
| `--wasi-modules=experimental-wasi-sockets` | Network socket access |

```bash
# Full capability example
wasmtime \
    --dir=/var/data \
    --env DATABASE_URL=... \
    --wasi-modules=experimental-wasi-sockets \
    my_service.wasm
```

---

## Edge Deployments

MatchBox WASM output targets WASI and can run on edge platforms:

### Fastly Compute

```bash
fastly compute build --source my_service.wasm
fastly compute deploy
```

### Cloudflare Workers (WASI)

```bash
# Use Cloudflare's WASM worker with WASI support
wrangler deploy --compatibility-flags=nodejs_compat
```

---

## Use Cases

| Scenario | Why WASM |
|----------|---------|
| Serverless/FaaS | Near-zero cold start, no JVM |
| Edge computing | Runs close to users, sandboxed |
| Microservices | `FROM scratch` images < 1MB |
| Multi-tenant SaaS | Strong WASM sandboxing per tenant |
| Plugin systems | Load and unload WASM modules at runtime |

---

## Comparison: WASM vs Native Binary

| | `--target wasm` | `--target native` |
|---|---|---|
| Portable | ✅ Any WASM runtime | ❌ Single platform |
| Sandboxed | ✅ WASI sandbox | ❌ Full OS access |
| Cold start | < 10ms | < 5ms |
| File/network | Explicit grants | Full OS |
| Edge platforms | ✅ Fastly, CF Workers | ❌ |
| Docker image | `FROM scratch` + runtime shim | `FROM scratch` only |

---

## Checklist

- [ ] Use `matchbox --target wasm service.bxs` to produce the `.wasm` file
- [ ] Test locally with `wasmtime --dir=. service.wasm` before deploying
- [ ] Grant only the capabilities the service needs (`--dir`, `--env`, sockets)
- [ ] Build `FROM scratch` OCI images for minimal container footprint
- [ ] Use multi-stage Docker builds: compile with MatchBox, ship only the WASM
- [ ] For edge deploys, verify WASI compatibility with the target platform (Fastly, Cloudflare)