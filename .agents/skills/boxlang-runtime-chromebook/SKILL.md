---
name: boxlang-runtime-chromebook
description: "Use this skill when setting up or developing BoxLang on a Chromebook, including enabling Linux development environment, installing Java 21, installing BoxLang, setting up VS Code with the BoxLang extension, and troubleshooting ARM/Intel Chromebook issues."
---

# BoxLang on Chromebooks

## Overview

Chromebooks support full BoxLang development through the built-in Linux
development environment (Crostini — Debian container). Both Intel and ARM-based
Chromebooks are supported.

---

## Requirements

- Chromebook with 4GB RAM minimum (8GB+ recommended)
- Chrome OS with Linux development environment enabled
- At least 2GB free disk space
- OpenJDK 21+

---

## Step 1 — Enable Linux Development Environment

1. Open **Settings** → **Advanced** → **Developers**
2. Turn on **Linux development environment**
3. Follow the setup wizard (choose 8GB+ storage when prompted)
4. Wait for the Debian container to initialize

---

## Step 2 — Open Linux Terminal

- Press **Alt + Shift + T** to open the Terminal app
- Click the **Penguin** tab to access the Linux (Debian) environment

---

## Step 3 — System Setup

```bash
# Update system and install essential tools
sudo apt update && sudo apt full-upgrade -y

sudo apt install -y \
    curl wget git zip unzip \
    build-essential \
    software-properties-common \
    apt-transport-https \
    ca-certificates
```

---

## Step 4 — Install Java 21

### Option A — Direct package install (most Chromebooks)

```bash
sudo apt install -y openjdk-21-jdk
java -version
```

### Option B — Manual install (if package unavailable)

```bash
# Download Temurin JDK 21 (works on both x86_64 and aarch64/ARM)
ARCH=$(dpkg --print-architecture)
if [ "$ARCH" = "arm64" ]; then
    JDK_URL="https://github.com/adoptium/temurin21-binaries/releases/latest/download/OpenJDK21U-jdk_aarch64_linux_hotspot_21_latest.tar.gz"
else
    JDK_URL="https://github.com/adoptium/temurin21-binaries/releases/latest/download/OpenJDK21U-jdk_x64_linux_hotspot_21_latest.tar.gz"
fi

wget -O /tmp/jdk21.tar.gz "$JDK_URL"
sudo mkdir -p /opt/java
sudo tar -xzf /tmp/jdk21.tar.gz -C /opt/java
# Add to PATH in ~/.bashrc
echo 'export JAVA_HOME=$(ls -d /opt/java/jdk-21*)' >> ~/.bashrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
java -version
```

---

## Step 5 — Install BoxLang

```bash
# Download the BoxLang installer script
curl -fsSL https://downloads.ortussolutions.com/boxlang/install-boxlang.sh | bash

# Verify installation
boxlang --version

# Or use BoxLang Version Manager (BVM) for version management
curl -fsSL https://downloads.ortussolutions.com/bvm/install.sh | bash
source ~/.bashrc
bvm install latest
bvm use latest
boxlang --version
```

---

## Step 6 — Install VS Code

VS Code is available as a `.deb` package that integrates natively with
Chromebook's Linux environment:

```bash
# Download VS Code for Linux (Debian/Ubuntu)
curl -fsSL https://packages.microsoft.com/keys/microsoft.asc | \
    gpg --dearmor -o /usr/share/keyrings/microsoft.gpg

echo "deb [arch=amd64,arm64 signed-by=/usr/share/keyrings/microsoft.gpg] \
    https://packages.microsoft.com/repos/code stable main" | \
    sudo tee /etc/apt/sources.list.d/vscode.list

sudo apt update
sudo apt install -y code
```

Install the BoxLang VS Code extension:

```bash
code --install-extension ortus-solutions.vscode-boxlang
```

Or from VS Code Extensions panel: search **BoxLang**.

---

## Step 7 — Create Your First App

```bash
mkdir ~/my-boxlang-app && cd ~/my-boxlang-app

cat > hello.bxs << 'EOF'
println( "Hello from BoxLang on ChromeOS!" )
println( "Java version: " & createObject("java","java.lang.System").getProperty("java.version") )
EOF

boxlang hello.bxs
```

---

## Running the MiniServer (Web Development)

```bash
# Start the MiniServer in your project directory
mkdir ~/webapp && cd ~/webapp

cat > index.bxm << 'EOF'
<bx:output>
<html>
<body>
    <h1>BoxLang on ChromeOS!</h1>
    <p>Today: #dateFormat( now(), "long" )#</p>
</body>
</html>
</bx:output>
EOF

# Start the server on port 8080
boxlang-miniserver --port 8080 --webroot .

# Access in Chrome at: http://localhost:8080/
```

---

## ARM Chromebook Notes

- Use packages tagged `arm64` or `aarch64` for all downloads
- BoxLang JAR is architecture-neutral (Java bytecode) — the same JAR works on all platforms
- VS Code detects ARM automatically and installs the correct binary
- The Linux environment on ARM Chromebooks uses `dpkg --print-architecture` → `arm64`

---

## Troubleshooting

### `command not found: boxlang` after install

```bash
# Reload shell config
source ~/.bashrc

# Or check if BVM added PATH correctly
echo $PATH | grep -o '[^:]*boxlang[^:]*'

# Manual PATH add if needed
echo 'export PATH=$HOME/.bvm/current/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### `java` not found

```bash
# Verify JAVA_HOME is set
echo $JAVA_HOME
java -version

# If not set, locate the JDK and add to PATH
sudo update-alternatives --config java
```

### Port not accessible from Chrome browser

Chrome OS forwards Linux ports automatically for `localhost`. If not working:

- Ensure the server is listening on `0.0.0.0` or `127.0.0.1`
- Try restarting the Linux container: **Settings → Linux → Restart**
- Check Chrome OS Linux port forwarding settings

### Out of disk space

Linux environment storage can be expanded:
- **Settings → Linux → Disk size → Resize**

---

## Development Best Practices on Chromebook

1. **Store code in Linux home** (`~/`) — accessible from VS Code and terminal
2. **Use extension settings** to point BoxLang extension to the correct binary path
3. **Access files** from the Files app via **Linux files → penguin → home**
4. **Use `code .`** from the Linux terminal to open VS Code in current directory
5. **Keep extensions minimal** — Chromebook resources are limited; install only what you need