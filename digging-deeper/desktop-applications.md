---
description: Build cross-platform desktop applications with ColdBox and the BoxLang Desktop runtime.
icon: desktop
---

# Desktop Applications

ColdBox applications can run as fully functional **desktop apps** through the BoxLang Desktop runtime — same handlers, models, and views you already know, packaged for Windows, macOS, and Linux.

🚀 **BoxLang only.**

📖 **Runtime documentation:** [BoxLang Desktop Applications](https://boxlang.ortusbooks.com/getting-started/running-boxlang/desktop-applications)

## Quick Start

Use the official starter template:

```bash
# Create a new desktop app from the template
coldbox create app name=myDesktopApp skeleton=https://github.com/coldbox-templates/boxlang-desktop
```

The [boxlang-desktop template](https://github.com/coldbox-templates/boxlang-desktop) wires the BoxLang runtime, an embedded webview, and the build pipeline for packaging installers.

## How It Works

A BoxLang Desktop app is a ColdBox app served by the embedded runtime, rendered in a native webview window:

- Your ColdBox app runs **locally** — no external server needed
- Views render in a system webview (your existing HTML/CSS/JS works)
- BoxLang gives you OS-level access (filesystem, dialogs, menus) from the same codebase
- Package installers per platform with the template's build scripts

## When to Use It

- Internal tools that need offline capability
- Kiosk or point-of-sale applications
- Cross-platform utilities where a JVM is acceptable but a browser tab isn't
- Reusing an existing ColdBox codebase on the desktop

## See Also

- [Application Templates](../getting-started/application-templates.md)
- [Deployment](../reference/deployment/README.md)
- [BoxLang Desktop docs](https://boxlang.ortusbooks.com/getting-started/running-boxlang/desktop-applications)
