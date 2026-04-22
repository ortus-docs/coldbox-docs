---
name: boxlang-runtime-esp32
description: "Use this skill when deploying BoxLang to ESP32 microcontrollers using MatchBox's --target esp32 flag, flashing firmware (full-flash vs fast-deploy), using watch mode for rapid iteration, accessing hardware via BoxLang BIFs, detecting the ESP32 runtime, and understanding FreeRTOS task constraints."
---

# BoxLang on ESP32

## Overview

MatchBox can compile and deploy BoxLang scripts directly to **ESP32 microcontrollers** using `--target esp32`. BoxLang runs on FreeRTOS with a custom task stack, and bytecode is stored in a dedicated flash partition. Full firmware flashing is only needed on first setup; subsequent deploys update only the bytecode (~1 second).

---

## Prerequisites

```bash
# Install the ESP-IDF prerequisites (see espressif.com/getting-started)

# Install the Rust ESP32 toolchain
cargo install espup
espup install

# Install espflash (requires 3.3.0+)
cargo install espflash@3.3.0
```

---

## First Flash (Full Firmware)

The first deploy installs the BoxLang firmware onto the device:

```bash
matchbox app.bxs --target esp32 --chip esp32s3 --full-flash
```

- `--full-flash` installs the full firmware (bootloader, partition table, and ByteCode VM)
- Only needed on first use or firmware upgrades
- Takes 30–60 seconds

---

## Fast Deploy (Bytecode Only)

After the firmware is installed, subsequent deploys update only the bytecode partition:

```bash
matchbox app.bxs --target esp32 --chip esp32s3 --flash
```

- Updates only the `storage` partition at `0x110000`
- Takes ~1 second
- No firmware reinstall needed

---

## Watch Mode (Auto-Recompile + Reflash)

Use `--watch` for rapid iteration during development:

```bash
matchbox app.bxs --target esp32 --chip esp32s3 --flash --watch
```

- Watches `.bxs` files for changes
- Automatically recompiles and reflashes on save
- Starts `espflash monitor` to show serial output

---

## Supported Chips

| Chip | Flag |
|------|------|
| ESP32-S3 | `--chip esp32s3` |
| ESP32-C3 | `--chip esp32c3` |
| ESP32 (original) | `--chip esp32` |
| ESP32-S2 | `--chip esp32s2` |

---

## FreeRTOS Task Constraints

BoxLang runs as a FreeRTOS task with the following constraints:

| Property | Value |
|----------|-------|
| Task stack size | 48 KB |
| Bytecode partition | `storage` at `0x110000` |
| Partition size | 1 MB |
| CPU | Xtensa LX7 (S3) / RISC-V (C3) |

> Keep your BoxLang scripts lean — avoid deep call stacks and large in-memory data structures.

---

## Runtime Detection

Detect ESP32 environment in BoxLang code:

```js
var arch = server.os.arch

if ( arch == "xtensa" ) {
    // ESP32-S3 (Xtensa LX7)
    println( "Running on Xtensa ESP32" )
} else if ( arch == "riscv" ) {
    // ESP32-C3 (RISC-V)
    println( "Running on RISC-V ESP32" )
} else {
    // Running on desktop (JVM BoxLang or MatchBox native)
    println( "Running on: " & arch )
}
```

---

## Example Script

```js
// blink.bxs — Blink an LED on GPIO pin 2
var ledPin = 2

// Initialize GPIO
gpioSetMode( ledPin, "OUTPUT" )

// Blink loop
while ( true ) {
    gpioWrite( ledPin, 1 )  // LED on
    delay( 500 )
    gpioWrite( ledPin, 0 )  // LED off
    delay( 500 )
}
```

```bash
# Deploy and run
matchbox blink.bxs --target esp32 --chip esp32s3 --flash
```

---

## Serial Monitor

View serial output from the device:

```bash
# via espflash
espflash monitor

# Or let --watch mode handle it automatically
matchbox app.bxs --target esp32 --chip esp32s3 --flash --watch
```

---

## Flash Partition Layout

```
0x000000  Bootloader
0x008000  Partition Table
0x010000  BoxLang Firmware (VM + Rust runtime)
0x110000  Storage Partition (BoxLang Bytecode, 1MB)
```

The `--flash` flag targets only `0x110000` — no firmware reinstall.

---

## Development Workflow

```text
1. Write BoxLang script (box.bxs)
2. First-time: matchbox box.bxs --target esp32 --chip esp32s3 --full-flash
3. Iterate:    matchbox box.bxs --target esp32 --chip esp32s3 --flash --watch
4. Monitor serial output in the watch console
5. Test on desktop before deploying: matchbox box.bxs (interpreted)
```

---

## Checklist

- [ ] Install `espup` and `espflash` (3.3.0+) before first use
- [ ] Use `--full-flash` once per device to install firmware
- [ ] Use `--flash` (not `--full-flash`) for all subsequent code updates (~1 second)
- [ ] Use `--watch` during development for immediate recompile + redeploy cycles
- [ ] Check `server.os.arch` to detect `"xtensa"` or `"riscv"` at runtime
- [ ] Keep scripts within the 48 KB FreeRTOS stack limit (avoid deep recursion)
- [ ] Test logic on desktop with `matchbox app.bxs` before deploying to hardware