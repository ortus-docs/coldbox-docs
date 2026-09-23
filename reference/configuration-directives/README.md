---
description: Configuration directives index — every section of the ColdBox class at a glance.
icon: list
---

# Configuration Directives

All application configuration lives in the **ColdBox class** (`config/ColdBox.bx`, or `config/ColdBox.cfc` for CFML apps) inside its `configure()` method. Each top-level struct is a directive:

| Directive | Purpose | Page |
| --- | --- | --- |
| `coldbox` | Core framework behavior — app name, event name, handlers, caching, error handling | [ColdBox Directives](coldbox.md) |
| `conventions` | Override default folder/file conventions | [ColdBox Directives](coldbox.md#conventions) |
| `environments` | Per-environment config overrides | [ColdBox Directives](coldbox.md#environments) |
| `flash` | Flash scope storage | [ColdBox Directives](coldbox.md#flash) |
| `interceptors` / `interceptorSettings` | Registered interceptors and their config | [ColdBox Directives](coldbox.md#interceptors) |
| `layouts` / `layoutSettings` | Layout registrations and behavior | [ColdBox Directives](coldbox.md#layouts) |
| `settings` | Custom application settings | [ColdBox Directives](coldbox.md#settings) |
| `modules` | Module loading and behavior | [Modules & Module Settings](modules-and-settings.md) |
| `moduleSettings` | Per-module settings | [Modules & Module Settings](modules-and-settings.md#module-settings) |
| `cachebox` | Caching — see the CacheBox book for the full DSL | [CacheBox Directive](cachebox.md) |
| `logbox` | Logging — see the LogBox book for the full DSL | [LogBox Directive](logbox.md) |
| `wirebox` | Dependency injection — see the WireBox book for the full DSL | [WireBox Directive](wirebox.md) |

## Environment Variables & Java Properties

Any directive can be overridden externally without touching code: [System Settings](../system-settings.md).

## A Note on the Companion Frameworks

CacheBox, LogBox, and WireBox are standalone frameworks with their own books. ColdBox docs cover **how the `ColdBox` class wires them in**; their internal DSLs (cache providers, appenders, binder mappings) are documented in their own books — see [Companion Frameworks](../companion-frameworks.md).
