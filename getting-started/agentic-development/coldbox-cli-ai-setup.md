---
description: Set up AI coding assistants on a ColdBox project with coldbox ai install — agents, guidelines, skills, and MCP servers.
icon: terminal
---

# ColdBox CLI AI Setup

The ColdBox CLI gives AI coding assistants deep knowledge of your project, its modules, and the BoxLang/CFML ecosystem — all living in a `.agents/` directory at your project root.

## Install the CLI

```bash
box install coldbox-cli
```

## Set Up AI Integration

```bash
# Interactive wizard
coldbox ai install

# Or bake it into a new app
coldbox create app myApp --ai
```

The wizard asks you to:

1. Choose your AI agents (Claude, Copilot, Cursor, Codex, Gemini, Kilo Code, OpenCode, Pi)
2. Select your project language (BoxLang, CFML, or Hybrid)
3. Configure guidelines, skills, and MCP servers

{% hint style="warning" %}
If existing agent files (`AGENTS.md`, `CLAUDE.md`, `.cursorrules`) weren't created by the CLI, you'll be prompted to **Overwrite**, **Merge** (CLI section prepended, your content preserved), or **Skip**. Use `--force` to overwrite without prompting.
{% endhint %}

## What Gets Created

```
.agents/
├── guidelines/          # Framework documentation
│   ├── core/           # boxlang.md, cfml.md, coldbox.md
│   ├── modules/        # Auto-discovered from installed modules
│   ├── custom/         # Your project-specific guidelines
│   └── overrides/      # Your customized versions
├── skills/             # Task cookbooks from skills.boxlang.io
├── mcp-servers/        # MCP server configurations
└── manifest.json       # AI integration metadata
```

Agent config files land at your project root: `CLAUDE.md` for Claude, `AGENTS.md` shared by Copilot/Codex/OpenCode/Kilo/Pi, `.cursorrules` for Cursor, `GEMINI.md` for Gemini.

## Everyday Commands

```bash
coldbox ai refresh      # Sync after installing modules
coldbox ai info         # Show current configuration
coldbox ai doctor       # Health check
coldbox ai stats        # Context consumption with token estimates
coldbox ai uninstall    # Remove AI integration
```

## Keep It in Sync

Run `coldbox ai refresh` whenever you install or update modules — module-provided guidelines and skills are auto-discovered and inventoried, and MCP documentation servers are matched to what's installed.

## Go Deeper

Module authoring, custom skills, the override system, and team workflows are covered in the full guide: [Agentic ColdBox](../../digging-deeper/ai/agentic-coldbox.md).

## See Also

- [Skills & Guidelines](skills-and-guidelines.md)
- [cbMCP](cbmcp.md) — live app introspection
- [ColdBox CLI](../coldbox-cli.md) — full command reference
