---
name: commandbox-config-settings
description: "Use this skill for CommandBox global configuration: config set/show/clear commands, server defaults, ForgeBox API tokens, endpoint settings, proxy configuration, module settings, env var overrides (box_config_*), setting sync across machines, task runner settings, tab completion style, preferred browser, artifacts directory, and nativeShell configuration."
---

# CommandBox Config Settings

## Overview

CommandBox has a global configuration file at `~/.CommandBox/CommandBox.json`. Manage it with `config set`, `config show`, and `config clear` commands.

```bash
# Open config file location
config show
# → shows path to CommandBox.json
```

---

## Core Config Commands

```bash
# Set a value
config set settingName=value

# Show a value
config show settingName

# Show all settings
config show

# Clear / remove a setting
config clear settingName

# Nested settings (dot notation)
config set modules.myModule.mySetting=foo
config show modules.myModule.mySetting
config clear modules.myModule.mySetting

# Array notation
config set myArray[1]=firstItem
config show myArray[1]

# Set multiple at once
config set setting1=a setting2=b setting3=c

# Set complex JSON value
config set myArray="['a','b','c']"
config set myStruct="{ 'key': 'value' }"

# Append to existing array/struct (same type required)
config set myArray="['d']" --append

# JMESPath filtering in config show
config show 'jq:{name:name, modules:modules}'
config show 'jq:keys(modules)'
config show "jq:key_contains(modules,'commandbox')"
```

---

## Server Defaults

Set default server start settings applied when not overridden by a specific `server.json`:

```bash
# Common server defaults
config set server.defaults.openBrowser=false
config set server.defaults.profile=production
config set server.defaults.jvm.heapSize=1024
config set server.defaults.jvm.heapSize=2G
config set server.defaults.web.rewrites.enable=true
config set server.defaults.web.directoryBrowsing=false
config set server.defaults.web.http.port=8080
config set server.defaults.trayEnable=false

# Show all server defaults
config show server.defaults
```

---

## ForgeBox API Token & Endpoints

```bash
# Set ForgeBox API token (obtain from forgebox.io account)
config set endpoints.forgebox.APIToken=your-secret-api-token

# Login (sets token automatically)
forgebox login

# Register a custom ForgeBox Enterprise endpoint
forgebox endpoint register myCompany https://forge.mycompany.com/api/v1

# Set token for enterprise endpoint
config set endpoints.forgebox-myCompany.APIToken=company-token
config set endpoints.forgebox-myCompany.APIURL=https://forge.mycompany.com/api/v1

# Show all endpoints
forgebox endpoint list
config show endpoints
```

---

## Module Settings

Override defaults for any installed module:

```bash
# Set module setting
config set modules.myModule.verbose=true
config set modules.myModule.apiUrl=https://api.example.com
config set modules.myModule.timeout=30

# Show module settings
config show modules.myModule

# Clear a module setting
config clear modules.myModule.verbose
```

---

## Proxy Configuration

For environments behind a corporate HTTP proxy:

```bash
config set proxy.server=proxy.example.com
config set proxy.port=8080
config set proxy.user=myuser
config set proxy.password=mypassword

# Show proxy settings
config show proxy
```

---

## Misc Settings

```bash
# Use custom native shell (default: /bin/sh)
config set nativeShell=/bin/zsh
config set nativeShell=/bin/bash

# Custom artifacts cache directory
config set artifactsDirectory=/fast-ssd/commandbox-artifacts

# Preferred browser for opening URLs
config set preferredBrowser=chrome    # chrome, firefox, edge, safari, opera

# Enable ANSI colors in non-interactive terminals (CI/CD)
config set colorInDumbTerminal=true

# Disable auto-update checks
config set autoUpdateCheck=false

# Git tag prefix for bump command
config set tagPrefix=''        # removes 'v' prefix (default: 'v')
config set tagVersion=false    # don't auto-tag on bump

# Inline tab completion (restart required)
config set tabCompleteInline=true
```

---

## Env Var Overrides

Any config setting can be overridden with OS environment variables without modifying `CommandBox.json`. Useful for CI/CD pipelines.

**Convention**: prefix with `box_config_` and use underscores for nested keys.

```bash
# Simple setting
export box_config_colorInDumbTerminal=true

# ForgeBox API token
export box_config_endpoints_forgebox_APIToken=my-token-here

# Nested with dots (Windows cmd allows special chars)
box_config_endpoints.forgebox.APIToken=my-token-here

# Complex JSON value
export box_config_proxy='{"server":"proxy.corp.com","port":8080}'

# Module setting
export box_config_modules_myModule_verbose=true

# Hyphenated module names (use full JSON)
export box_config_modules='{"commandbox-bullet-train":{"showGitBranch":true}}'
```

> Env var overrides **do not** persist to `CommandBox.json` and are lost when the shell exits. They override any explicitly set values.

### Java System Property Overrides (CLI JVM)

```bash
# Pass as -D flag when starting box
box -Dbox_config_colorInDumbTerminal=true

# Or in commandbox.properties
jvm.args=-Dbox_config_endpoints_forgebox_APIToken=my-token
```

---

## Setting Sync

Sync your config settings across machines using your preferred source control or cloud sync:

```bash
# Export all settings to a file
config show --json > my-commandbox-settings.json

# Import settings from a file
recipe my-commandbox-settings.boxr

# Or use symlinks to sync CommandBox.json via Dropbox/iCloud/Git
# ln -s ~/Dropbox/commandbox/CommandBox.json ~/.CommandBox/CommandBox.json
```

---

## Task Runner Settings

```bash
# Set default task file name convention
config set taskRunner.taskFile=task

# Set default target
config set taskRunner.target=run
```

---

## Complete Config Reference

| Setting | Type | Description |
|---------|------|-------------|
| `server.defaults` | struct | Global server.json defaults |
| `endpoints.forgebox.APIToken` | string | ForgeBox API token |
| `endpoints.forgebox.APIURL` | string | Custom ForgeBox API URL |
| `modules.<name>.*` | struct | Per-module settings |
| `proxy.server` | string | HTTP proxy hostname |
| `proxy.port` | number | HTTP proxy port |
| `proxy.user` | string | Proxy username |
| `proxy.password` | string | Proxy password |
| `nativeShell` | string | Shell for OS commands (default: `/bin/sh`) |
| `artifactsDirectory` | string | Package cache location |
| `preferredBrowser` | string | Default browser for `openURL()` |
| `colorInDumbTerminal` | boolean | ANSI colors in CI/non-TTY |
| `autoUpdateCheck` | boolean | Check for CommandBox updates on start |
| `tagVersion` | boolean | Auto-tag Git repo on `bump` |
| `tagPrefix` | string | Tag prefix for `bump` (default: `v`) |
| `tabCompleteInline` | boolean | Inline tab completion style |
| `developerMode` | boolean | Reload shell before each command (dev only) |