---
name: boxlang-runtime-compiled-native-binaries
description: "Use this skill when compiling BoxLang scripts to standalone native executables using MatchBox's --target native flag, cross-compiling for multiple platforms, optimizing binary size, and using Native Fusion to expose Rust functions as BoxLang built-in functions."
---

# BoxLang Compiled Native Binaries

## Overview

MatchBox can compile BoxLang source code to standalone native executables using `--target native`. The resulting binary embeds a small Rust runner stub (~500 KB) with the compiled BoxLang bytecode appended, requiring no JVM or MatchBox installation on the target machine.

---

## Building a Native Binary

```bash
# Compile script to a native executable (same OS/arch as build machine)
matchbox --target native app.bxs
# Output: ./app  (or app.exe on Windows)

# Run it
./app --config=prod.json --debug
```

The binary accepts the same CLI argument format as the `matchbox` runner.

---

## Cross-Compilation

Build for multiple platforms using GitHub Actions (recommended) or the `cross` Rust tool:

### GitHub Actions (5 targets in parallel)

```yaml
# .github/workflows/release.yml
name: Build Native Binaries

on:
  push:
    tags: ["v*"]

jobs:
  build:
    strategy:
      matrix:
        include:
          - os: ubuntu-latest
            target: x86_64-unknown-linux-gnu
            artifact: app-linux-x64
          - os: ubuntu-latest
            target: aarch64-unknown-linux-gnu
            artifact: app-linux-arm64
          - os: macos-latest
            target: x86_64-apple-darwin
            artifact: app-macos-x64
          - os: macos-latest
            target: aarch64-apple-darwin
            artifact: app-macos-arm64
          - os: windows-latest
            target: x86_64-pc-windows-msvc
            artifact: app-windows-x64.exe

    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4

      - name: Install MatchBox
        run: curl -sSL https://raw.githubusercontent.com/ortus-boxlang/matchbox/master/install/install.sh | bash

      - name: Compile
        run: matchbox --target native --arch ${{ matrix.target }} app.bxs -o ${{ matrix.artifact }}

      - uses: actions/upload-artifact@v4
        with:
          name: ${{ matrix.artifact }}
          path: ${{ matrix.artifact }}
```

### Cross-compilation with `cargo cross`

```bash
cargo install cross

# Linux ARM64 from macOS or Linux x64
cross build --release --target aarch64-unknown-linux-gnu
```

---

## Binary Size Optimizations

The default release binary is ~500 KB. You can reduce further with these Rust profile settings in `Cargo.toml`:

```toml
[profile.release]
opt-level = "z"     # Optimize for size (not speed)
lto = true          # Link-time optimization
codegen-units = 1   # Single codegen unit (better LTO)
strip = true        # Strip debug symbols from binary
```

---

## Native Fusion — Rust BIFs

Native Fusion lets you write Rust functions and expose them as BoxLang BIFs inside a native binary. This is for performance-critical operations that can't be done efficiently in BoxLang:

```rust
// src/main.rs
use matchbox_sdk::prelude::*;

fn fast_hash(value: &str) -> String {
    // your Rust implementation
    format!("{:x}", md5::compute(value))
}

fn main() {
    let engine = MatchBoxEngine::new()
        .register_bifs(vec![
            bif!("FastHash", fast_hash)
        ])
        .run_file("app.bxs");
}
```

```js
// app.bxs — calls the Rust BIF
var hash = FastHash( "hello world" )
println( hash )
```

Build as a combined Rust + BoxLang executable:

```bash
cargo build --release
# Output: target/release/myapp
```

---

## Distribution Patterns

### Single self-contained binary

```bash
matchbox --target native app.bxs -o myapp
# Distribute: just the `myapp` file, no dependencies needed
```

### Docker with minimal image

```dockerfile
FROM scratch
COPY myapp /myapp
ENTRYPOINT ["/myapp"]
```

Results in an image with only the executable — < 1MB total.

---

## Checklist

- [ ] Test with `matchbox app.bxs` (interpreted) before compiling to native
- [ ] Use GitHub Actions matrix to build all 5 platform targets
- [ ] Apply `opt-level="z"`, `lto=true`, `strip=true` for minimal binary size
- [ ] Use Native Fusion for computationally intensive operations (hashing, encoding, parsing)
- [ ] Distribute as `FROM scratch` Docker images for microservice deployments
- [ ] Validate cross-compiled binaries on each target platform before release