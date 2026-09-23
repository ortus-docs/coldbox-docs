---
description: WireBox, CacheBox, and LogBox — ColdBox's embedded companion frameworks and their documentation.
icon: cubes
---

# Companion Frameworks

ColdBox embeds three standalone frameworks, each with its own complete documentation. **ColdBox docs cover how the framework consumes them; their books cover what they can do.**

## WireBox — Dependency Injection & AOP

The container behind `getInstance()`, `property inject`, and every model in your app.

📖 [wirebox.ortusbooks.com](https://wirebox.ortusbooks.com)

- [Configuring WireBox](https://wirebox.ortusbooks.com/configuration/configuring-wirebox) — binder DSL and properties
- [ColdBox Enhanced Binder](https://wirebox.ortusbooks.com/configuration/configuring-wirebox/coldbox-enhanced-binder) — ColdBox-specific binder additions
- [Mapping DSL](https://wirebox.ortusbooks.com/configuration/mapping-dsl) — mapping objects, scopes, and AOP

ColdBox-side integration: [Models](../the-basics/models/README.md) · [Injection DSL](../the-basics/models/injection-dsl/README.md) · [WireBox Directive](configuration-directives/wirebox.md)

## CacheBox — Enterprise Caching

The engine behind event caching, view caching, and `cachebox:` injections.

📖 [cachebox.ortusbooks.com](https://cachebox.ortusbooks.com)

- [CacheBox DSL](https://cachebox.ortusbooks.com/configuration/cachebox-configuration/cachebox-dsl) — declaring caches
- [Cache Providers](https://cachebox.ortusbooks.com/usage/cache-providers) — engine-specific providers
- [ColdBox Configuration](https://cachebox.ortusbooks.com/configuration/cachebox-configuration/coldbox-configuration) — the ColdBox integration

ColdBox-side integration: [Event Caching](../the-basics/event-handlers/event-caching.md) · [View Caching](../the-basics/layouts-and-views/views/view-caching.md) · [HTTP Caching](../digging-deeper/http-caching.md) · [CacheBox Directive](configuration-directives/cachebox.md)

## LogBox — Logging & Messaging

The engine behind `logbox:` injections and framework logging.

📖 [logbox.ortusbooks.com](https://logbox.ortusbooks.com)

- [LogBox DSL](https://logbox.ortusbooks.com/configuration/configuring-logbox/logbox-dsl) — configuring loggers
- [Adding Appenders](https://logbox.ortusbooks.com/configuration/configuring-logbox/adding-appenders) — file, console, DB, and more

ColdBox-side integration: [LogBox Directive](configuration-directives/logbox.md)

## MCP Documentation Servers

All three books (and this one) are queryable from AI assistants via MCP:

```
https://wirebox.ortusbooks.com/~gitbook/mcp
https://cachebox.ortusbooks.com/~gitbook/mcp
https://logbox.ortusbooks.com/~gitbook/mcp
https://coldbox.ortusbooks.com/~gitbook/mcp
```

See [Agentic Development](../getting-started/agentic-development/README.md) for wiring these into your AI agents automatically.
