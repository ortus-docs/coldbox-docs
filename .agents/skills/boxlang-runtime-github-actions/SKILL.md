---
name: boxlang-runtime-github-actions
description: "Use this skill when setting up GitHub Actions CI/CD pipelines for BoxLang projects, including the setup-boxlang action, supported inputs/outputs, installing modules, CommandBox integration, multi-engine testing, and complete workflow templates."
---

# BoxLang in GitHub Actions

## Overview

The `ortus-boxlang/setup-boxlang@main` action installs and configures BoxLang
(and optionally CommandBox) in a GitHub Actions runner. It handles version
resolution, optional module installation, ForgeBox authentication and module
caching.

---

## Quick Setup

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@main
  with:
    version: latest          # or "snapshot" or "1.x.y"
```

---

## Action Inputs

| Input | Default | Description |
|-------|---------|-----------|
| `version` | `latest` | BoxLang version: `latest`, `snapshot`, or semver `1.2.3` |
| `modules` | — | Space-separated list of BoxLang modules to install |
| `with-commandbox` | `false` | Also install CommandBox CLI |
| `commandbox_version` | `latest` | CommandBox version: `latest` or semver |
| `commandbox_modules` | — | Comma-separated CommandBox modules |
| `forgeboxAPIKey` | — | ForgeBox API key for private modules or higher rate limits |
| `boxlang_home` | `~/.boxlang` | Override BoxLang home directory |

---

## Action Outputs

| Output | Description |
|--------|-----------|
| `boxlang-version` | BoxLang version installed |
| `installation-path` | Path to the BoxLang installation directory |

---

## Basic Workflow — Run Scripts

```yaml
name: CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup BoxLang
        uses: ortus-boxlang/setup-boxlang@main
        with:
          version: latest

      - name: Run tests
        run: boxlang tests/run.bxs
```

---

## Installing BoxLang Modules

```yaml
- name: Setup BoxLang with modules
  uses: ortus-boxlang/setup-boxlang@main
  with:
    version: latest
    modules: bx-compat-cfml bx-mail bx-pdf
```

---

## CommandBox + TestBox Integration

```yaml
name: TestBox CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup BoxLang + CommandBox
        uses: ortus-boxlang/setup-boxlang@main
        with:
          version: latest
          with-commandbox: true
          commandbox_modules: testbox-cli

      - name: Install dependencies
        run: box install

      - name: Run TestBox
        run: box testbox run reporter=json outputFile=./test-results.json

      - name: Upload test results
        uses: actions/upload-artifact@v4
        with:
          name: test-results
          path: test-results.json
```

---

## Multi-Platform Matrix

```yaml
name: Cross-Platform CI

on: [push, pull_request]

jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        boxlang-version: [latest, snapshot]

    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v4

      - name: Setup BoxLang
        uses: ortus-boxlang/setup-boxlang@main
        with:
          version: ${{ matrix.boxlang-version }}

      - name: Run tests
        run: boxlang tests/run.bxs
```

---

## Private Modules with ForgeBox API Key

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@main
  with:
    version: latest
    forgeboxAPIKey: ${{ secrets.FORGEBOX_API_KEY }}
    modules: my-private-module
```

---

## Snapshot Testing (Nightly Builds)

```yaml
name: Snapshot Test

on:
  schedule:
    - cron: "0 5 * * *"   # Every day at 05:00 UTC

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup BoxLang snapshot
        uses: ortus-boxlang/setup-boxlang@main
        with:
          version: snapshot

      - name: Run tests
        run: boxlang tests/run.bxs
```

---

## Full CI/CD Pipeline

```yaml
name: BoxLang CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    env:
      FORGEBOX_API_KEY: ${{ secrets.FORGEBOX_API_KEY }}

    steps:
      - uses: actions/checkout@v4

      - name: Setup BoxLang + CommandBox
        uses: ortus-boxlang/setup-boxlang@main
        id: setup-boxlang
        with:
          version: latest
          with-commandbox: true
          commandbox_modules: testbox-cli
          forgeboxAPIKey: ${{ secrets.FORGEBOX_API_KEY }}
          modules: bx-mail bx-pdf

      - name: Report versions
        run: |
          echo "BoxLang: ${{ steps.setup-boxlang.outputs.boxlang-version }}"
          echo "Install : ${{ steps.setup-boxlang.outputs.installation-path }}"
          box version

      - name: Install project dependencies
        run: box install

      - name: Run unit tests
        run: box testbox run reporter=junit outputFile=./reports/junit.xml

      - name: Upload test report
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: test-report
          path: reports/

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup BoxLang + CommandBox
        uses: ortus-boxlang/setup-boxlang@main
        with:
          version: latest
          with-commandbox: true
          forgeboxAPIKey: ${{ secrets.FORGEBOX_API_KEY }}

      - name: Bump version and publish
        run: box run-script release
        env:
          FORGEBOX_API_KEY: ${{ secrets.FORGEBOX_API_KEY }}
```

---

## Checklist

- [ ] Use `secrets.FORGEBOX_API_KEY` instead of hard-coding keys
- [ ] Pin action as `ortus-boxlang/setup-boxlang@main` (or at a specific SHA for reproducibility)
- [ ] Cache dependencies with `actions/cache` for faster builds when using CommandBox
- [ ] Upload test results with `actions/upload-artifact` for visibility
- [ ] Use the `snapshot` version in a nightly schedule job to catch regressions early