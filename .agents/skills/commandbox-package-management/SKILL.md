---
name: commandbox-package-management
description: "Use this skill for CommandBox package management: box.json configuration, installing packages from ForgeBox/Git/HTTP/folder, semantic versioning, dependencies and devDependencies, updating packages, lock files, package scripts, private packages, publishing to ForgeBox, creating packages, and code endpoints."
---

# CommandBox Package Management

## Overview

CommandBox is a full-featured package manager for CFML/BoxLang projects. Packages are hosted on [ForgeBox](https://www.forgebox.io) and can also be installed from Git, HTTP/S, local folders, S3, or jar files.

---

## `box.json` — Package Descriptor

Every package has a `box.json` in its root. Initialize one with:

```bash
# Quick init
init

# Init with properties
init name="My App" slug=my-app version=1.0.0 author="Your Name"

# Interactive wizard
init --wizard
```

### Full `box.json` Reference

```json
{
    "name": "My BoxLang App",
    "slug": "my-boxlang-app",
    "version": "1.0.0",
    "author": "Jane Doe <jane@example.com>",
    "location": "",
    "directory": "",
    "createPackageDirectory": true,
    "packageDirectory": "",
    "homepage": "https://example.com",
    "documentation": "https://docs.example.com",
    "repository": {
        "type": "git",
        "URL": "https://github.com/org/my-app"
    },
    "bugs": "https://github.com/org/my-app/issues",
    "shortDescription": "A brief description",
    "description": "Full description or leave empty and add README.md",
    "type": "projects",
    "keywords": ["boxlang", "web"],
    "private": false,
    "engines": [
        { "type": "boxlang", "version": ">=1.0.0" }
    ],
    "license": [
        { "type": "Apache-2.0", "URL": "https://www.apache.org/licenses/LICENSE-2.0" }
    ],
    "dependencies": {
        "coldbox": "^7.0.0",
        "cbvalidation": "4.x"
    },
    "devDependencies": {
        "testbox": "^5.0.0"
    },
    "installPaths": {
        "coldbox": "modules/coldbox"
    },
    "ignore": [
        ".git",
        "tests",
        "workbench"
    ],
    "scripts": {
        "postInstall": "migrate up",
        "postUpdate": "migrate up",
        "build": "task run workbench/build"
    },
    "testbox": {
        "runner": "http://localhost:8080/tests/runner.cfm",
        "verbose": false,
        "watchDelay": 1000,
        "watchPaths": "/models/**.cfc"
    }
}
```

### Reading/Writing box.json Properties

```bash
# Read properties
package show
package show name
package show version
package show dependencies

# Write properties
package set name="New Name"
package set version=2.0.0
package set description="My app"

# Append to arrays
package set keywords="['newKeyword']" --append
```

---

## Installing Packages

```bash
# Install latest stable from ForgeBox
install coldbox

# Install specific version
install coldbox@7.0.0

# Install latest even if pre-release (bleeding edge)
install coldbox@be

# Install any patch of version 7 (7.x)
install coldbox@7.x

# Install with semantic range
install "coldbox@>6.0.0 <=7.5.0"
install coldbox@~7.1       # patch-level changes
install coldbox@^7.0.0     # compatible release

# Install without saving to box.json
install coldbox --noSave

# Install as dev dependency
install testbox --saveDev

# Install all dependencies from box.json
install

# Install only production dependencies (skip devDependencies)
install --production

# Install to a specific directory
install coldbox directory=./lib/

# Verbose output
install coldbox --verbose
```

### Installing from Different Endpoints

```bash
# From Git (GitHub, GitLab, Bitbucket)
install git+https://github.com/org/my-module.git
install git+https://github.com/org/my-module.git#v1.0.0   # tag
install git+https://github.com/org/my-module.git#main      # branch

# From HTTP/HTTPS URL (zip file)
install https://example.com/mypackage.zip

# From local folder
install folder:../my-local-module

# From local file
install file:/path/to/my-module.zip

# From S3
install s3://my-bucket/packages/mymodule.zip

# From Gist
install gist:abcdef1234567890

# JAR via HTTP
install jar:https://example.com/my.jar

# Java package from Maven
install java:org.apache.commons:commons-lang3:3.12.0
```

---

## Semantic Versioning

| Range | Meaning |
|-------|---------|
| `*` or `x` | Latest stable |
| `1.2.3` | Exact version |
| `^1.2.3` | Compatible: `>=1.2.3 <2.0.0` |
| `~1.2` | Approx: `>=1.2.0 <1.3.0` |
| `>1.5.0` | Greater than |
| `>=1.0 <=2.0` | Range |
| `1.2 - 3.2` | Between (inclusive) |
| `4.x` | Any 4.* version |
| `@be` | Bleeding edge (latest including pre-release) |
| `@stable` | Latest stable only |

---

## Dependencies vs devDependencies

```bash
# Regular dependency (required at runtime)
install coldbox

# Dev dependency (testing/build tools only)
install testbox --saveDev

# Uninstall (removes from box.json)
uninstall coldbox

# Uninstall without removing from box.json
uninstall coldbox --noSave
```

---

## Updating Packages

```bash
# Update all packages to latest satisfying version range
update

# Update specific package
update coldbox

# Update and save new version to box.json
update coldbox --force

# Check what's outdated
outdated
outdated --verbose
```

---

## Lock Files

```bash
# Generate a package-lock.json file
package lock

# Install from lock file (exact versions)
install --frozen

# Update lock file
package lock --force
```

---

## Package Scripts

Scripts in `box.json` run automatically at defined lifecycle events:

```json
{
    "scripts": {
        "preInstall": "echo 'Before install'",
        "postInstall": "migrate up",
        "prePublish": "task run build",
        "postPublish": "echo 'Published!'",
        "build": "task run workbench/build",
        "test": "testbox run"
    }
}
```

```bash
# Run a script manually
run-script build
run-script test

# List available scripts
run-script --list
```

**Lifecycle hooks** (auto-fired):
- `preInstall` / `postInstall`
- `preUpdate` / `postUpdate`
- `preUninstall` / `postUninstall`
- `prePublish` / `postPublish`
- `onRelease`

---

## Artifacts Cache

CommandBox caches downloaded packages locally for offline use:

```bash
# List cached artifacts
artifacts list

# Clean all cached artifacts
artifacts clean

# Remove specific artifact
artifacts remove coldbox

# Show artifact location
artifacts list --verbose
```

---

## Creating and Publishing Packages

```bash
# Initialize as publishable package
package init slug=my-package type=projects

# Publish to ForgeBox (requires API token)
publish

# Publish a specific version
package version patch    # 1.0.0 -> 1.0.1
package version minor    # 1.0.0 -> 1.1.0
package version major    # 1.0.0 -> 2.0.0
publish
```

### Set ForgeBox API Token

```bash
config set endpoints.forgebox.APIToken=your-forgebox-api-key
```

---

## Private Packages

```bash
# Mark package as private in box.json
package set private=true

# Install private package (must be authenticated)
install my-private-package
```

---

## System Modules

System modules install globally to `~/.CommandBox/cfml/modules`:

```bash
# Install as system module
install commandbox-cfconfig --system

# List system modules
list --system
```

---

## Managing Versions

```bash
# Bump version (semantic)
package version patch    # x.x.X
package version minor    # x.X.0
package version major    # X.0.0

# Set specific version
package set version=2.3.1

# Show current version
package show version
```