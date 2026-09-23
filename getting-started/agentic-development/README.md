---
description: AI-assisted development for ColdBox — CLI tooling, skills, guidelines, MCP servers, and the BoxLang IDE.
icon: robot
---

# Agentic Development

ColdBox is built for the agentic era. AI coding agents like Claude Code, GitHub Copilot, Cursor, Codex, and OpenCode can generate entire features, debug complex issues, and refactor at speed — but their effectiveness depends on how well they understand your codebase and framework.

## Why ColdBox for AI Development?

ColdBox's opinionated conventions make it ideal for AI-assisted development. When an agent adds a handler, it knows exactly where it goes. Routes, models, views, and modules follow predictable patterns, so agents generate idiomatic ColdBox code instead of generic guesses.

On top of those conventions, ColdBox ships a complete agent toolkit:

| Tool | What it gives your agent |
| --- | --- |
| **[ColdBox CLI AI](coldbox-cli-ai-setup.md)** | Project-local guidelines, skills, and agent configs via `coldbox ai install` |
| **[Skills & Guidelines](skills-and-guidelines.md)** | 200+ task cookbooks from [skills.boxlang.io](https://skills.boxlang.io) plus framework conventions |
| **[cbMCP](cbmcp.md)** | Live introspection of your *running* application over MCP (BoxLang only) |
| **[BoxLang IDE](boxlang-ide.md)** | VS Code tooling with language intelligence and AI integration |

## Quick Start

```bash
# 1. Get the ColdBox CLI
box install coldbox-cli

# 2. Add AI integration to your project (interactive wizard)
coldbox ai install

# 3. Or scaffold a new app with AI baked in
coldbox create app myApp --ai
```

The wizard detects your AI agents, installs framework guidelines and skills into `.agents/`, and wires up MCP documentation servers. Your agent immediately knows ColdBox conventions, BoxLang syntax, and your installed modules.

```bash
# Keep everything in sync as you add modules
coldbox ai refresh
```

## The Four Pillars

1. **Guidelines** — ColdBox, BoxLang, and CFML conventions stored on-disk in `.agents/guidelines/`, kept lean so agents load them on demand
2. **Skills** — step-by-step implementation cookbooks (build a CRUD handler, a REST API, a test suite) sourced from the central registry
3. **Agents** — ready-made configurations for Claude, Copilot, Cursor, Codex, Gemini, Kilo Code, OpenCode, and Pi
4. **MCP Servers** — 30+ documentation servers auto-matched to your installed modules, plus live app introspection via cbMCP

## See Also

- [AI Runtime Capabilities](../../digging-deeper/ai/README.md) — `toAi()`, `toMCP()`, `toAiGateway()`, and BoxLang AI for building AI *features* (as opposed to AI-assisted development)
- [ColdBox CLI](../coldbox-cli.md) — the full CLI reference
