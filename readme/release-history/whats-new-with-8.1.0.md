---
description: April 14, 2026
---

# What's New With 8.1.0

ColdBox 8.1.0 is a minor release that brings targeted improvements, bug fixes, and new features across the ColdBox platform, WireBox, and CacheBox.

## Major Highlights

### 🤖 AI & MCP Routing — `toAi()` and `toMCP()`

ColdBox now ships with two powerful new routing terminators that bring **first-class BoxLang AI and MCP support** directly into the router. Think of `toAi()` as the AI equivalent of `resources()` — a single line of routing code that automatically scaffolds a complete, standardized REST API for any BoxLang `IAiRunnable` so you can build chat agents, embedding endpoints, or custom AI tools in seconds. Meanwhile, `toMCP()` exposes any BoxLang MCP server as an HTTP endpoint, making it easy to connect your BoxLang tools and resources to external AI assistants like Claude or GitHub Copilot.

#### `toAi()` — Auto-scaffolded AI REST API

Calling `.toAi( runnable )` on any route pattern registers **four sub-endpoints automatically**:

| Verb   | Endpoint            | Description                                                        |
| ------ | ------------------- | ------------------------------------------------------------------ |
| `POST` | `{base}/invoke`     | Synchronous execution — calls `runnable.run( input, params, options )` and returns JSON |
| `POST` | `{base}/stream`     | SSE streaming — calls `runnable.stream()` and pushes chunks via BoxLang's `SSE()` BIF  |
| `POST` | `{base}/batch`      | Batch execution — runs the runnable over an array of `inputs[]` in parallel, returning all results |
| `GET`  | `{base}/info`       | Self-describing metadata — returns the runnable name, description, and a list of all generated endpoints |

The `runnable` can be a **WireBox ID string** (resolved lazily at request time) or a **live `IAiRunnable` instance**. All route modifiers you chain before `toAi()` — conditions, domain restrictions, SSL, headers — are automatically inherited by every sub-route.

```javascript
// One line → four REST endpoints for your AI agent
route( "/api/chat" ).toAi( "MyChatAgent" );

// Using a direct WireBox instance
route( "/api/embeddings" ).toAi( getInstance( "EmbeddingRunnable" ) );

// With auth guard inherited by all four sub-routes
route( "/api/chat" )
    .withCondition( ( route, params, event ) => event.isAuthenticated() )
    .toAi( "MyChatAgent" );
```

The generated `invoke` endpoint accepts a JSON body with `input`, `params`, and `options` keys. The `stream` endpoint uses Server-Sent Events (SSE) and sends `chunk` events as the response streams. The `batch` endpoint accepts an `inputs[]` array and returns an `outputs[]` array, with per-item error recovery. The `info` endpoint is a self-discovery endpoint any client can call to learn what is available.

{% hint style="info" %}
`toAi()` requires BoxLang as the active runtime and the `bxai` module installed (`box install bxai`). Both are verified at route-registration time so misconfigurations surface on startup, not at request time.
{% endhint %}

#### `toMCP()` — Expose MCP Servers Over HTTP/HTTPS

`toMCP()` exposes a registered BoxLang **Model Context Protocol (MCP) server** as an HTTP endpoint that any MCP-compatible AI client (Claude, GitHub Copilot, Cursor, etc.) can connect to. The entire HTTP request is delegated to the server's `MCPRequestProcessor`, making your BoxLang tools, resources, and prompts available to external AI assistants.

```javascript
// Expose a named MCP server on a fixed route
route( "/mcp/filesystem" ).toMCP( "FileSystemServer" );

// With an auth condition
route( "/mcp/database" )
    .withCondition( ( route, params, event ) => event.isAuthenticated() )
    .toMCP( "DatabaseServer" );

// Dynamic — resolve the server name from the :mcpServer URL placeholder
route( "/mcp/:mcpServer" ).toMCP();
```

Combined, these two terminators mean you can go from zero to a fully-functional, streaming-capable AI API **and** an MCP-compliant tool server with just a few lines of routing configuration.

### ⏰ Scheduler Enhancements

Schedulers now expose two new properties — `started` (boolean) and `startedAt` (datetime) — so you can programmatically check whether a scheduler is running and when it was started. This is especially useful for health checks and cluster-aware task management.

### 🔒 Cluster Lock Algorithm Upgrade

The scheduled task server fixation algorithm for cluster locking has been upgraded, resolving long-standing edge cases when tasks are distributed across multiple servers.

### 🌐 BoxLang Engine Agnosticism

Several internal CF-specific variable names and entries have been renamed to engine-agnostic alternatives, further aligning ColdBox with BoxLang as a first-class runtime and reducing CFML-only assumptions in the codebase.

### 🗑️ Module `cfmapping` Deprecation

`this.cfmapping` in `ModuleConfig.cfc` is now deprecated in favor of `this.classMapping`, which works consistently across both CFML and BoxLang runtimes.

## Release Notes

The full release notes per library can be found below. Just click on the library tab and explore their release notes:

{% tabs %}
{% tab title="ColdBox" %}
### New Features

[COLDBOX-1378](https://ortussolutions.atlassian.net/browse/COLDBOX-1378) Schedulers now have a `started` and `startedAt` properties

[COLDBOX-1382](https://ortussolutions.atlassian.net/browse/COLDBOX-1382) `toAi()` and `toMCP()` routing for BoxLang AI Routables

### Improvements

[COLDBOX-1376](https://ortussolutions.atlassian.net/browse/COLDBOX-1376) AbstractCacheProvider updates to interface completion and fine tuning

[COLDBOX-1377](https://ortussolutions.atlassian.net/browse/COLDBOX-1377) Update to use new docbox types

[COLDBOX-1383](https://ortussolutions.atlassian.net/browse/COLDBOX-1383) Check whether the object is already in JSON format to avoid double serialization

[COLDBOX-1386](https://ortussolutions.atlassian.net/browse/COLDBOX-1386) Renaming of dedicated CF variables and entries to engine-agnostic ones for BoxLang

### Bugs

[COLDBOX-1047](https://ortussolutions.atlassian.net/browse/COLDBOX-1047) Scheduled tasks server fixation upgraded algorithm for cluster lock

[COLDBOX-1370](https://ortussolutions.atlassian.net/browse/COLDBOX-1370) `renderData` not sending encoding with content-type header

[COLDBOX-1375](https://ortussolutions.atlassian.net/browse/COLDBOX-1375) Whoops using external URL for alpinejs instead of local one

[COLDBOX-1380](https://ortussolutions.atlassian.net/browse/COLDBOX-1380) `MockRequestContext.getResponseHeaders()` returns empty struct in integration tests after `setup()`

[COLDBOX-1384](https://ortussolutions.atlassian.net/browse/COLDBOX-1384) ColdBox proxy not disallowing `.bxm` proxies

### Tasks

[COLDBOX-1387](https://ortussolutions.atlassian.net/browse/COLDBOX-1387) Deprecate `this.cfmapping` on module configuration and use `this.classMapping` instead
{% endtab %}

{% tab title="WireBox" %}
### New Features

[WIREBOX-160](https://ortussolutions.atlassian.net/browse/WIREBOX-160) Threading concurrent modification issues in `Mapping.cfc`

### Improvements

[WIREBOX-159](https://ortussolutions.atlassian.net/browse/WIREBOX-159) Race condition when loading namespaced modules
{% endtab %}

{% tab title="CacheBox" %}
### Bugs

[CACHEBOX-93](https://ortussolutions.atlassian.net/browse/CACHEBOX-93) `statusCheck()` typo not calling `isEnabled()`

### Improvements

[CACHEBOX-92](https://ortussolutions.atlassian.net/browse/CACHEBOX-92) `AbstractCacheProvider` `set...` methods should have numeric timeout defaults
{% endtab %}
{% endtabs %}
