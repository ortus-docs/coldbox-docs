---
description: >-
  cbMCP exposes your running ColdBox application as a live Model Context
  Protocol (MCP) server, giving any AI client real-time introspection
  into routing, handlers, modules, WireBox, CacheBox, LogBox, and more.
icon: server
---

# ColdBox MCP Server (cbMCP)

{% hint style="warning" %}
`cbMCP` is a **BoxLang-only** module. It requires BoxLang 1.x, ColdBox 8+, and the `bx-ai` module.
{% endhint %}

`cbMCP` is a ColdBox module that turns your running application into a fully-compliant [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server. AI clients — Claude Desktop, VS Code Copilot, Cursor, Codex, or any MCP-capable tool — can connect and gain live, read-only introspection across the entire ColdBox platform layer: routing, handlers, modules, dependency injection, caching, logging, scheduling, and more.

```text
┌──────────────────────────────────────────────────────────────────────┐
│                        Your ColdBox Application                      │
│                                                                      │
│  ┌─────────────┐  ┌─────────────────────────────────────────────┐    │
│  │  cbMCP      │  │              MCP Tool Registry              │    │
│  │  Module     │  │                                             │    │
│  │             │  │  SystemTools   HandlerTools  RoutingTools   │    │
│  │  /cbmcp     │  │  ModuleTools   WireBoxTools  CacheBoxTools  │    │
│  │  (endpoint) │  │  LogBoxTools   SchedulerTools AsyncTools    │    │
│  │             │  │  InterceptorTools                           │    │
│  └──────┬──────┘  └─────────────────────────────────────────────┘    │
│         │                                                            │
└─────────┼────────────────────────────────────────────────────────────┘
          │  HTTP / SSE (MCP protocol)
          │
    ┌─────▼──────────────────────────────────────────────┐
    │              MCP Clients                           │
    │                                                    │
    │  Claude Desktop    VS Code Copilot    Cursor IDE   │
    │  GitHub Codex      Gemini CLI         OpenCode     │
    │  Any HTTP/SSE MCP client                           │
    └────────────────────────────────────────────────────┘
```

---

## How It Works

```mermaid
sequenceDiagram
    autonumber
    participant IDE as AI Client (Claude / Copilot)
    participant MCP as cbMCP Endpoint (/cbmcp)
    participant ColdBox as ColdBox Services
    participant Tools as Tool Classes

    IDE->>MCP: MCP initialize handshake
    MCP-->>IDE: capabilities + tools list (50+ tools)

    IDE->>MCP: tools/call { name: "list_routes" }
    MCP->>Tools: RoutingTools.list_routes()
    Tools->>ColdBox: getRoutingService().getRouter().getRoutes()
    ColdBox-->>Tools: routes[ ]
    Tools-->>MCP: structured JSON
    MCP-->>IDE: tool result

    IDE->>MCP: resources/read { uri: "coldbox://app/settings" }
    MCP->>ColdBox: getColdBoxSettings()
    ColdBox-->>MCP: settings struct
    MCP-->>IDE: resource content
```

---

## Installation

```bash
# Step 1 — BoxLang AI runtime (required dependency)
box install bx-ai

# Step 2 — cbMCP module
box install cbmcp
```

Once installed and the application boots, the MCP endpoint is **immediately live** at:

```text
http://<host>:<port>/cbmcp
// If you enable SSL, it will be available at, which we recommend for secure AI client connections:
https://<host>:<port>/cbmcp
```

No routing configuration is needed — cbMCP registers its own entry point at `cbmcp` via `ModuleConfig.bx`.

---

## Configuration

The module automatically registers itself at the `/cbmcp` endpoint with the following default settings:

- **CORS**: Enabled for all origins (`*`)
- **Statistics**: Enabled (collects usage metrics)
- **Entry Point**: `/cbmcp`

No manual routing or configuration is required. Once installed and your application boots, the MCP endpoint is live immediately.

---

## Connecting an AI Client

### Claude Desktop

Add the following entry to your Claude Desktop configuration:

**macOS:** `~/Library/Application Support/Claude/claude_desktop_config.json`
**Windows:** `%APPDATA%\Claude\claude_desktop_config.json`

```json
// If using HTTP Redirects (local development only):
{
  "mcpServers": {
    "cbMCP": {
      "command": "npx",
      "args": [
        "-y",
        "@depasquale/mcp-http-stdio-bridge",
        "--url",
        "http://127.0.0.1:<port>/cbmcp"
      ]
    }
  }
}
```

```json
// If using HTTP/s
{
  "mcpServers": {
    "cbMCP": {
      "type": "http",
      "url": "https://127.0.0.1:<port>/cbmcp"
    }
  }
}
```

Replace `<port>` with your application's port (e.g. `60299` for a local BoxLang dev server). The `@depasquale/mcp-http-stdio-bridge` package is installed automatically by `npx` on first use — no manual install required.

Restart Claude Desktop after saving. The `cbMCP` server will appear in the tools panel.

### VS Code / GitHub Copilot

Add to your `.vscode/mcp.json` (project-scoped) or your user-level VS Code MCP settings:

```json
{
  "servers": {
    "cbMCP": {
      "type": "http",
      "url": "https://127.0.0.1:<port>/cbmcp"
    }
  }
}
```

### Any MCP-Compatible Client (HTTP / SSE transport)

Point the client directly at the SSE endpoint:

```text
https://<host>:<port>/cbmcp
```

---

## Available Tools

`cbMCP` ships with **10 tool classes**, auto-discovered at startup. Every function annotated with `@mcpTool` is surfaced to the connected AI client.

### System (`SystemTools`)

| Tool | Description |
| ---- | ----------- |
| `time_now` | Returns the server's current date/time (ISO 8601) |
| `get_coldbox_settings` | Returns all ColdBox framework settings |
| `get_runtime_info` | Engine name, version, app layout, and OS |
| `get_application_structure` | Application directory layout (handlers, models, views, etc.) |
| `get_setting( name )` | Returns a single ColdBox application setting by key |
| `reinit_app` | Reinitialises the ColdBox application |

### Handlers (`HandlerTools`)

| Tool | Description |
| ---- | ----------- |
| `get_handlers` | Lists all registered event handlers with their actions |
| `get_handler( handlerName, [moduleName] )` | Returns metadata for a specific handler |

### Routing (`RoutingTools`)

| Tool | Description |
| ---- | ----------- |
| `list_routes` | Lists all registered application routes |
| `list_module_routes` | Lists which modules have routes registered |
| `get_module_routes( moduleName )` | Returns routes registered by a specific module |
| `get_router_settings` | Returns router configuration (default route, SSL, etc.) |

### Modules (`ModuleTools`)

| Tool | Description |
| ---- | ----------- |
| `get_modules` | Returns full metadata for all registered modules |
| `get_loaded_modules` | Returns a flat list of activated module names |
| `get_module( moduleName )` | Returns metadata for a specific module |

### WireBox (`WireBoxTools`)

| Tool | Description |
| ---- | ----------- |
| `get_wirebox_mappings` | Returns all registered DI mappings |
| `has_mapping( name )` | Checks whether a mapping exists |
| `get_mapping( name )` | Returns the full mapping definition for a binding |

### CacheBox (`CacheBoxTools`)

| Tool | Description |
| ---- | ----------- |
| `get_caches` | Returns all registered cache providers with stats |
| `get_cache_names` | Returns a flat list of cache provider names |
| `get_cache_stats( cacheName )` | Returns hit/miss/size statistics for a cache |
| `get_cache_keys( cacheName )` | Lists all keys currently in a cache |
| `get_cache_size( cacheName )` | Returns the number of objects in a cache |
| `get_cache_key_metadata( cacheName, objectKey )` | Returns metadata for a single cached entry |
| `cache_key_exists( cacheName, objectKey )` | Checks whether a key exists in a cache |
| `get_store_metadata_report( cacheName )` | Returns full store metadata for all entries |
| `clear_all( cacheName )` | Clears all entries from a cache |
| `clear_item( cacheName, objectKey )` | Clears a single entry by key |
| `clear_all_events( cacheName, [async] )` | Clears all event-cached entries |
| `clear_event( cacheName, eventSnippet, [queryString] )` | Clears a specific event cache entry |
| `clear_event_multi( cacheName, eventSnippets, [queryString] )` | Clears multiple event cache entries |
| `clear_all_views( cacheName, [async] )` | Clears all view-cached entries |
| `clear_view( cacheName, viewSnippet )` | Clears a specific view cache entry |
| `clear_view_multi( cacheName, viewSnippets )` | Clears multiple view cache entries |

### LogBox (`LogBoxTools`)

| Tool | Description |
| ---- | ----------- |
| `get_logbox_info` | Returns LogBox version, ID, loggers, and appenders |
| `get_loggers` | Returns all registered logger definitions |
| `get_logger( category )` | Returns the logger definition for a category |
| `get_appenders` | Returns all registered appender definitions |
| `get_root_logger` | Returns the ROOT logger definition |
| `read_log_entries( [appenderName], [limit=100] )` | Reads the last N entries from file-based appenders |
| `get_last_error( [appenderName] )` | Finds the most recent ERROR or FATAL entry in log files |

### Interceptors (`InterceptorTools`)

| Tool | Description |
| ---- | ----------- |
| `get_interceptors` | Returns all registered interceptors |
| `get_interceptor_states` | Returns all registered interception points and their listeners |

### Schedulers (`SchedulerTools`)

| Tool | Description |
| ---- | ----------- |
| `get_schedulers` | Returns all registered application schedulers and their tasks |
| `get_scheduler( schedulerName )` | Returns metadata for a specific scheduler |
| `get_task_stats( schedulerName, taskName )` | Returns execution stats for a specific task |
| `run_task( schedulerName, taskName )` | Executes a scheduled task on demand |
| `pause_task( schedulerName, taskName )` | Pauses a scheduled task |
| `resume_task( schedulerName, taskName )` | Resumes a paused scheduled task |

### Async (`AsyncTools`)

| Tool | Description |
| ---- | ----------- |
| `get_executors` | Returns all registered async executors with their stats |
| `get_executor_names` | Returns a flat list of executor names |

---

## MCP Resources

`cbMCP` also exposes **MCP Resources** — ambient read-only context that AI clients can read at any time, without calling a tool explicitly. Resources provide background knowledge that is automatically injected into AI conversations.

| URI | Description |
| --- | ----------- |
| `coldbox://app/settings` | All active ColdBox application configuration settings |
| `coldbox://app/modules` | All currently activated modules with key metadata |
| `coldbox://app/routes` | All registered application routes |
| `coldbox://app/handlers` | All registered event handlers |

---

## MCP Prompts

`cbMCP` ships with pre-packaged **MCP Prompts** — ready-made AI workflows that appear in the client's prompt library. Select one and the AI runs it against your live application automatically.

| Prompt | Description |
| ------ | ----------- |
| `coldbox_app_overview` | Generates a comprehensive overview of the running ColdBox application |
| `debug_handler` | Diagnoses issues with a specific event handler (requires `handlerName` arg) |
| `cache_health_report` | Produces a CacheBox health report across all cache providers |
| `interceptor_audit` | Audits all registered interceptors and interception points for conflicts and ordering issues |

---

## Security & Best Practices

### CORS Configuration

By default, cbMCP enables CORS for all origins (`*`). This is suitable for local development, but in production environments, consider restricting CORS to specific AI client domains:

**Custom CORS in ModuleConfig:**
```boxlang
mcpServer(
    name: "cbMCP",
    cors: "https://your-ai-platform.com",  // Restrict to specific domain
    ...
)
```

### HTTPS / SSL

**Always use HTTPS in production.** Set up SSL in your ColdBox application and configure AI clients to use the secure endpoint:

```text
https://your-domain.com/cbmcp
```

### Environment Variables

Store sensitive configuration in environment variables instead of hardcoding:

```bash
export CBMCP_ENABLED=true
export CBMCP_CORS_ORIGIN=https://your-ai-platform.com
```

Then reference in ModuleConfig:

```boxlang
cors: systemSettings.get( "CBMCP_CORS_ORIGIN", "*" )
```

---

## Performance & Statistics

cbMCP collects usage statistics by default (`statsEnabled: true`). Monitor these metrics via the `bx-ai` module's stats endpoint to:

- Track tool usage frequency
- Identify most-used features
- Monitor response times
- Detect performance bottlenecks

The module is read-only, so it has minimal performance impact. For large applications with heavy MCP traffic, consider:

- Implementing caching strategies in custom tools
- Optimizing data retrieval in resource handlers
- Using async executors for long-running operations

---

## Troubleshooting

### Connection Issues

**Problem:** Claude Desktop shows "Connection Failed"

1. Verify the endpoint is accessible: `curl https://127.0.0.1:<port>/cbmcp`
2. Check that ColdBox is running and the module is loaded
3. Review ColdBox logs for errors: `logs:boxlang` (or your server command)
4. Ensure the port number in your config matches your running application

**Problem:** CORS errors in browser console

- Verify the client is connecting via the correct protocol (HTTP/HTTPS)
- If behind a proxy, ensure the proxy forwards MCP protocol headers
- Check ModuleConfig CORS settings

### Module Not Loading

If `/cbmcp` endpoint is not available:

1. Verify `cbmcp` is installed: `box list`
2. Verify `bx-ai` module dependency is installed: `box list | grep bx-ai`
3. Reinitialize the application: `?fwreinit=1`
4. Check application logs for startup errors

### Tool Not Available

If a tool is registered but not showing in the AI client:

1. Verify the tool class has both `@AITool` and `@mcpTool` annotations
2. Check that the function is public (not private)
3. Restart Claude Desktop to refresh the tools list
4. Check the ColdBox debug output to confirm auto-scan picked up the tool

---

## Advanced Configuration

### Custom Module Configuration

Extend cbMCP by overriding ModuleConfig settings in your application:

```boxlang
// In your app's ColdBox.cfc configure() method:
moduleSettings = {
    cbMCP = {
        // Custom settings here
    }
}
```

### Monitoring Tool Calls

View all MCP operations via the stats endpoint (if stats are enabled):

```text
GET /api/cbmcp/stats
```

Returns detailed metrics on tool calls, resources accessed, and performance data.

---

## Creating Custom Tools

You can extend cbMCP by adding your own tool classes. Create a BoxLang class anywhere in your application and annotate functions with `@AITool` and `@mcpTool`. The function's docblock becomes the tool's description visible to the AI client.

```javascript
// models/tools/MyAppTools.bx
class {

    property name="productService" inject="ProductService";

    /**
     * Get the 10 best-selling products by revenue.
     */
    @AITool
    @mcpTool
    function get_top_products() {
        return productService.getTopSellers( limit: 10 );
    }

    /**
     * Get a single product by its SKU.
     *
     * @sku The product SKU to look up (e.g. "COLD-001")
     */
    @AITool
    @mcpTool
    function get_product( required string sku ) {
        return productService.getBySKU( arguments.sku );
    }

}
```

{% hint style="info" %}
Both `@AITool` and `@mcpTool` annotations are required. `@AITool` registers the function with the `bx-ai` tool registry; `@mcpTool` marks it for MCP protocol discovery and exposure.
{% endhint %}

Get access to the MCP server and register more tools (https://ai.ortusbooks.com/advanced/mcp-server#tool-registration) or [prompts](https://ai.ortusbooks.com/advanced/mcp-server#prompt-registration) or [resources](https://ai.ortusbooks.com/advanced/mcp-server#resource-registration)!

```javascript
// Get the cbMCP server instance from BoxLang AI
mcpServer( "cbMCP" )
    // Register a new tool class
    .registerTool(
        aiTool( "getWeather", "Get current weather for a location", ( location ) => {
            return weatherService.getCurrent( location )
        } )
        .describeArg( "location", "City name or coordinates" )
    )
    // Register an array of tools
    .registerTools( [
        aiTool( "getNews", "Get latest news headlines", ( topic ) => {
            return newsService.getHeadlines( topic )
        } ).describeArg( "topic", "News topic or category" ),
        aiTool( "getTime", "Get current server time", () => {
            return new Date().toISOString();
        } )
    ] )
    // Scan my own tools
    .scan( "models.aitools" )
    // Scan my single tool
    .scan( "my.tool.Here" )
```

---

## Architecture Deep Dive

```mermaid
graph TB
    subgraph cbMCP["cbMCP Module (onLoad)"]
        MCPReg["mcpServer() Registration"]
        ToolScan["Auto-scan @mcpTool functions"]
        ResScan["Register Resources"]
        PromptReg["Register Prompts"]
    end

    subgraph Tools["Tool Classes (models/tools/)"]
        ST["SystemTools"]
        HT["HandlerTools"]
        RT["RoutingTools"]
        MT["ModuleTools"]
        WT["WireBoxTools"]
        CT["CacheBoxTools"]
        LT["LogBoxTools"]
        IT["InterceptorTools"]
        SCH["SchedulerTools"]
        AT["AsyncTools"]
    end

    subgraph Protocol["MCP Protocol Layer (bx-ai)"]
        Init["initialize"]
        TList["tools/list"]
        TCall["tools/call"]
        RRead["resources/read"]
        PGet["prompts/get"]
    end

    subgraph Clients["AI Clients"]
        Claude["Claude Desktop"]
        Copilot["VS Code Copilot"]
        Cursor["Cursor / Codex"]
    end

    MCPReg --> ToolScan
    MCPReg --> ResScan
    MCPReg --> PromptReg

    ToolScan --> ST & HT & RT & MT & WT & CT & LT & IT & SCH & AT

    Protocol --> cbMCP
    Clients --> Protocol

    style cbMCP fill:#1e3a5f,stroke:#4a90d9,color:#fff
    style Tools fill:#2d5016,stroke:#6abf40,color:#fff
    style Protocol fill:#3d1a1a,stroke:#c94040,color:#fff
    style Clients fill:#2a2a4a,stroke:#7070cc,color:#fff
```

---

## What AI Clients Can Do With It

Once connected, an AI client with `cbMCP` access can answer questions and perform tasks that previously required a developer at their terminal:

```
"What routes does my app have that match /api/users?"
  → tools/call list_routes

"Which WireBox mappings are singletons?"
  → tools/call get_wirebox_mappings

"Show me the hit rate of the `template` cache."
  → tools/call get_cache_stats { cacheName: "template" }

"Are there any ERROR entries in my logs in the last hour?"
  → tools/call get_last_error

"What scheduled tasks are registered, and when did they last run?"
  → tools/call get_schedulers

"Run the nightly-cleanup task now."
  → tools/call run_task { schedulerName: "AppScheduler", taskName: "nightly-cleanup" }
```

---

## Source & Changelog

- **GitHub**: [https://github.com/coldbox-modules/cbmcp](https://github.com/coldbox-modules/cbmcp)
- **ForgeBox**: `box install cbmcp`
