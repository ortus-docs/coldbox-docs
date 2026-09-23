---
description: cbMCP — expose your running ColdBox application as an MCP server so AI agents can introspect it live.
icon: satellite-dish
---

# cbMCP — Live App Introspection

**cbMCP** turns your running ColdBox application into a fully-compliant MCP server. Any MCP-capable AI client (Claude, Copilot, Cursor) can connect and get live answers about your app — no terminal, no log-diving, no code grepping.

🚀 **BoxLang only.**

```bash
box install cbMCP
```

## What Agents Can Ask

Once connected, your AI assistant can answer questions like:

- "What routes does my app expose under `/api`?"
- "Show me all WireBox singletons."
- "Are there any ERROR log entries in the last hour?"
- "Run the nightly-cleanup scheduler task now."

cbMCP ships with **10 tool classes (50+ tools)** covering routing, handlers, WireBox, CacheBox, LogBox, schedulers, and more — plus MCP Resources (ambient context auto-injected into conversations) and MCP Prompts (ready-made workflows).

## Setup

```bash
box install cbMCP
```

No extra configuration is required. Once the module is installed and your application boots, the MCP endpoint is live at `http://<host>:<port>/cbmcp`.

Connect your MCP-compatible client to it — for stdio-based clients like Claude Desktop, use the HTTP bridge:

```json
{
    "mcpServers" : {
        "coldbox" : {
            "command" : "npx",
            "args"    : [ "-y", "@depasquale/mcp-http-stdio-bridge", "--url", "http://127.0.0.1:60299/cbmcp" ]
        }
    }
}
```

VS Code and Cursor can connect to the HTTP endpoint directly in their MCP settings.

## Go Deeper

Full tool reference, resources, prompts, and security configuration: [ColdBox MCP Server](../../digging-deeper/ai/coldbox-mcp-server.md).

## See Also

- [AI Runtime Capabilities](../../digging-deeper/ai/README.md)
- [ColdBox CLI AI Setup](coldbox-cli-ai-setup.md)
