---
name: commandbox-setup
description: "Use this skill for installing, configuring, and upgrading CommandBox CLI: Homebrew install on Mac, apt-get on Linux, Windows install, Java requirements, custom home directory, upgrading CommandBox, light/thin binaries, non-Oracle JRE setup, and first-run configuration."
---

# CommandBox Setup & Installation

## Overview

CommandBox is a standalone CLI tool for Windows, Mac, and Linux that provides a command line interface for CFML/BoxLang development, package management, embedded servers, and automation. Written in CFML on top of WireBox, Undertow, and Lucee.

- **Java requirement**: Java 11+ required (Java 21+ recommended for BoxLang runtime)
- **Disk space**: 250MB+ free
- **RAM**: 256MB+ for CLI (servers require additional memory)
- **Home directory**: `~/.CommandBox/` (or customized via `commandbox_home`)

---

## Installation

### macOS — Homebrew (Recommended)

```bash
# Stable release
brew install commandbox

# Bleeding edge
brew tap ortus-solutions/homebrew-boxtap
brew install --head ortus-solutions/homebrew-boxtap/commandbox

# Upgrade
brew upgrade commandbox

# Switch between versions
brew install commandbox@5.1.1
brew unlink commandbox
brew link commandbox@5.1.1
```

After install, run `box` to complete the one-time unpacking:

```bash
box
```

### Linux — apt-get

```bash
# Debian/Ubuntu setup (libappindicator for tray icon)
sudo apt install libappindicator3-dev    # Ubuntu 18.04+
sudo apt install libappindicator-dev     # Older Ubuntu/Debian

# Add Ortus repo and install
curl -fsSl https://downloads.ortussolutions.com/debs/gpg | sudo apt-key add -
echo "deb https://downloads.ortussolutions.com/debs/noarch /" | sudo tee /etc/apt/sources.list.d/commandbox.list
sudo apt-get update && sudo apt-get install commandbox

# RPM / yum
curl -fsSl https://downloads.ortussolutions.com/KEYS | sudo rpm --import -
echo -e "[commandbox]\nname=CommandBox\nbaseurl=https://downloads.ortussolutions.com/rpms/noarch\nenabled=1\ngpgcheck=1\ngpgkey=https://downloads.ortussolutions.com/KEYS" | sudo tee /etc/yum.repos.d/commandbox.repo
sudo yum install commandbox
```

### Windows

1. Download `box.exe` from [downloads.ortussolutions.com](https://downloads.ortussolutions.com)
2. Place `box.exe` in a directory on your `PATH`
3. Run `box.exe` to complete first-time unpacking
4. On Windows 10+: If SmartScreen blocks, click "More info" → "Run anyway"

### Manual Install (Any Platform)

```bash
# Download the appropriate binary, unzip and place in PATH
# macOS/Linux: /usr/local/bin/box
# Run to unpack
box
```

---

## Custom Home Directory

By default, CommandBox unpacks into `~/.CommandBox`. Override with:

**Command-line flag:**
```bash
box -commandbox_home=/custom/path
```

**`commandbox.properties` file** (place in same directory as the binary):
```properties
commandbox_home=/custom/path
# or relative path
commandbox_home=../boxHome
```

> Note: On Homebrew installs, place `commandbox.properties` in `/opt/homebrew/Cellar/commandbox/<version>/libexec/bin/`

---

## Light & Thin Binaries

CommandBox offers smaller binaries for environments where size matters:

| Binary | Description |
|--------|-------------|
| Standard | Full install with all core modules |
| Light | No server or forgebox modules bundled |
| Thin | No modules bundled — all downloaded on first run |

```bash
# Light binary: download from releases page with "-light" suffix
# Thin binary: download with "-thin" suffix
```

---

## Java Version Management

CommandBox embeds its own JRE or uses the system JRE:

```bash
# Check what Java CommandBox is using
box java version

# List available JDKs
box java list

# Install a specific JDK
box java install openjdk21

# Set a custom JRE path (place 'jre' folder next to the box binary)
# Or set JAVA_HOME env var before running box
```

For non-Oracle JREs (e.g., OpenJDK, Azul Zulu):

```bash
# Set JAVA_HOME to point to your JDK
export JAVA_HOME=/path/to/openjdk21
box
```

---

## Upgrading CommandBox

```bash
# From within CommandBox shell
upgrade

# Or via Homebrew on macOS
brew upgrade commandbox

# Check current version
version
```

> **Warning**: Homebrew upgrade erases the current Cellar folder. Back up `commandbox.properties` before upgrading.

---

## First-Run Configuration

```bash
# Enter interactive shell
box

# Check version
version

# Update to latest
upgrade

# Set ForgeBox API key (for publishing packages)
config set endpoints.forgebox.APIToken=your-api-key
```

---

## Verify Installation

```bash
# Check version
box version

# Run a quick command
box echo "CommandBox is working!"

# List installed modules
box list --system
```