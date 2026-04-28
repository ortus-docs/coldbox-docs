---
description: >-
  The ColdBox CLI has tons of AI features to help build context and skills for
  any LLM and Agent
icon: terminal
---

# Agentic ColdBox

## Introduction

Agentic ColdBox supercharges your development workflow by providing comprehensive AI assistance for both **BoxLang** and **CFML** applications through the ColdBox CLI.

The system combines four key components:

1. **Guidelines** - Framework documentation stored locally in `.agents/guidelines/core/` and referenced via `read_file`
2. **Skills** - On-demand coding cookbooks sourced from [skills.boxlang.io](https://skills.boxlang.io)
3. **Agents** - AI assistant configurations (Claude, Copilot, Cursor, Codex, Gemini, OpenCode)
4. **MCP Servers** - Context protocol servers tracked in `.mcp.json` for live documentation and application introspection

{% hint style="info" %}
Make sure you are on the latest `coldbox-cli` in your CommandBox installation before getting started.
{% endhint %}

***

## Installation

```bash
# Install ColdBox CLI (if not already installed)
box install coldbox-cli

# Set up AI integration with an interactive wizard
coldbox ai install
```

The wizard guides you through agent selection, language detection, and MCP server configuration. After installation, the following structure is created:

```
.agents/
├── guidelines/
│   ├── core/        # 3 core guidelines: coldbox.md, boxlang.md, cfml.md
│   ├── custom/      # Your project-specific guidelines
│   └── overrides/   # Override core guidelines
├── skills/          # Installed skills (one folder per skill)
│   └── {name}/
│       └── SKILL.md
├── mcp-servers/     # MCP server configurations
└── manifest.json    # AI integration metadata
.mcp.json            # Project MCP server registry (project root)
```

Agent configuration files are generated automatically:

| Agent              | Config File          |
| ------------------ | -------------------- |
| **Claude**         | `CLAUDE.md`          |
| **GitHub Copilot** | `AGENTS.md` (shared) |
| **Cursor**         | `.cursorrules`       |
| **Codex**          | `AGENTS.md` (shared) |
| **Gemini**         | `GEMINI.md`          |
| **OpenCode**       | `AGENTS.md` (shared) |

{% hint style="info" %}
After installation, add your project context to the generated agent file — business domain, key services, authentication approach, and API endpoints. This gives AI assistants the application-specific knowledge they need.
{% endhint %}

### Keeping Resources Updated

```bash
# Sync everything after installing or updating modules
coldbox ai refresh
```

Automate with CommandBox scripts:

```json
{
  "scripts": {
    "postInstall": "coldbox ai refresh",
    "postUpdate":  "coldbox ai refresh"
  }
}
```

***

## Core Concepts

### Guidelines vs Skills

**Guidelines** teach AI agents *what the framework is and how it works* — architecture, conventions, and API references. They answer: *"What tools do I have?"*

**Skills** teach AI agents *how to do specific things* — step-by-step cookbooks with working code patterns. They answer: *"How do I build this exact feature?"*

Core guidelines (ColdBox + language) live on-disk in `.agents/guidelines/core/` and are referenced via `read_file` in agent files — keeping agent files lean (~250 lines) while maintaining full framework knowledge. Skills are sourced from [skills.boxlang.io](https://skills.boxlang.io) and loaded on-demand.

***

## AI Guidelines

Three core guidelines are installed automatically:

| Guideline   | File         | Description                                    |
| ----------- | ------------ | ---------------------------------------------- |
| **coldbox** | `coldbox.md` | ColdBox framework architecture and conventions |
| **boxlang** | `boxlang.md` | BoxLang language features and syntax           |
| **cfml**    | `cfml.md`    | CFML language fundamentals                     |

### Custom Guidelines

Add project-specific guidelines for your business domain, third-party integrations, or team conventions:

```bash
# Create a custom guideline
touch .agents/guidelines/custom/payment-processing.md
```

### Overriding Guidelines

```bash
# Override a core guideline with your own version
coldbox ai guidelines install coldbox --override
```

***

## AI Skills

Skills are sourced from [skills.boxlang.io](https://skills.boxlang.io) — the official registry for ColdBox, BoxLang, TestBox, CommandBox, and module skills. Browse the full catalog there. Install them via:

```bash
# Install a skill
coldbox ai skills install creating-handlers

# List installed skills
coldbox ai skills list
coldbox ai skills list --verbose
```

### Custom Skills

Create project-specific skills for domain workflows:

```bash
mkdir -p .agents/skills/deploying-to-kubernetes
touch .agents/skills/deploying-to-kubernetes/SKILL.md
```

***

## AI Agents

ColdBox AI Integration supports **6 major AI agents** with automatic configuration generation:

| Agent              | Config File          | Description                    |
| ------------------ | -------------------- | ------------------------------ |
| **Claude**         | `CLAUDE.md`          | Claude Desktop and Claude Code |
| **GitHub Copilot** | `AGENTS.md` (shared) | VS Code Copilot integration    |
| **Cursor**         | `.cursorrules`       | Cursor IDE rules               |
| **Codex**          | `AGENTS.md` (shared) | Codex AI assistant             |
| **Gemini**         | `GEMINI.md`          | Gemini CLI integration         |
| **OpenCode**       | `AGENTS.md` (shared) | OpenCode assistant             |

```bash
coldbox ai agents list                     # List available
coldbox ai agents add claude copilot       # Add agents
coldbox ai agents remove cursor            # Remove agent
coldbox ai agents refresh                  # Regenerate configs
```

***

## MCP Servers

Model Context Protocol (MCP) servers provide AI agents with live documentation and application introspection. All servers are tracked in **`.mcp.json`** at your project root.

### Installing the ColdBox Live MCP Server (cbMCP)

The `cbMCP` module turns your **running ColdBox application** into an MCP server, giving AI agents real-time access to routes, handlers, WireBox mappings, and more:

```bash
# Install cbMCP and register it in .mcp.json
coldbox ai mcp install

# With custom host/port
coldbox ai mcp install --host=localhost --port=8080
```

Once installed, AI agents can introspect your live app:

```
AI: "What routes does my app expose under /api?"
AI: "Show me all WireBox singletons."
AI: "Are there any ERROR log entries in the last hour?"
```

### Managing MCP Servers

```bash
coldbox ai mcp list                              # List registered servers
coldbox ai mcp add --name="myDocs" --url="..."  # Add a server
coldbox ai mcp remove myDocs                    # Remove a server
```

During `coldbox ai refresh`, the CLI auto-detects MCP servers from installed modules and updates `.mcp.json` automatically.

### Built-in MCP Servers

| Server            | Description                             |
| ----------------- | --------------------------------------- |
| `boxlang`         | BoxLang Language Documentation          |
| `boxlang-ide`     | BoxLang IDE Documentation               |
| `modern-cfml`     | Modern CFML Guide                       |
| `coldbox`         | ColdBox Framework Documentation         |
| `commandbox`      | CommandBox CLI Documentation            |
| `testbox`         | TestBox Testing Framework               |
| `wirebox`         | WireBox Dependency Injection            |
| `cachebox`        | CacheBox Caching Framework              |
| `logbox`          | LogBox Logging Framework                |
| `docbox`          | DocBox Documentation Generator          |
| `bxorm`           | BoxLang ORM                             |
| `cborm`           | ColdBox ORM Utilities                   |
| `qb`              | Query Builder (QB)                      |
| `quick`           | Quick ORM Active Record                 |
| `cfmigrations`    | Database Migrations                     |
| `cbsecurity`      | CBSecurity Authentication/Authorization |
| `cbauth`          | CBAuth User Authentication              |
| `cbsso`           | CBSSO Single Sign-On                    |
| `cbvalidation`    | CBValidation Validation Framework       |
| `cbi18n`          | CBI18N Internationalization             |
| `cbmailservices`  | CBMailServices Email Integration        |
| `cbdebugger`      | CBDebugger Debugging Tools              |
| `cbelasticsearch` | CBElasticsearch Integration             |
| `cbfs`            | CBFS File System Abstraction            |
| `cfconfig`        | CFConfig Server Configuration           |
| `cbwire`          | CBWire Reactive Components              |
| `cbq`             | CBQ Job Queues                          |
| `megaphone`       | Megaphone Messaging                     |
| `contentbox`      | ContentBox CMS                          |
| `relax`           | Relax REST API Documentation            |

***

## CLI Commands

### Setup & Management

```bash
coldbox ai install                  # Interactive installation wizard
coldbox ai info                     # Show current configuration
coldbox ai tree                     # Visual hierarchy of components
coldbox ai tree --verbose           # Include file paths
coldbox ai refresh                  # Sync with installed modules
```

### Component Management

```bash
# Guidelines
coldbox ai guidelines list                    # List installed
coldbox ai guidelines list --verbose          # With descriptions
coldbox ai guidelines install coldbox         # Install specific
coldbox ai guidelines uninstall coldbox       # Remove guideline

# Skills
coldbox ai skills list                        # List installed
coldbox ai skills list --verbose              # With descriptions
coldbox ai skills install creating-handlers   # Install specific
coldbox ai skills uninstall creating-handlers # Remove skill

# Agents
coldbox ai agents list                        # List available
coldbox ai agents add claude copilot          # Add agents
coldbox ai agents remove cursor               # Remove agent
coldbox ai agents refresh                     # Regenerate configs

# MCP Servers
coldbox ai mcp list                           # List servers
coldbox ai mcp add --name="x" --url="..."     # Add server
coldbox ai mcp remove myServer                # Remove server
coldbox ai mcp install                        # Install cbMCP live server
```

### Diagnostics

```bash
coldbox ai doctor                   # Check integration health
coldbox ai doctor --fix             # Auto-fix common issues
coldbox ai stats                    # Context usage overview
coldbox ai stats --verbose          # Detailed breakdown
```

***

## Best Practices

**Version Control** — commit your custom guidelines and skills to share them with your team:

```
# Commit these:
.agents/guidelines/custom/
.agents/skills/
.agents/manifest.json
.mcp.json
```

**Stay Synced** — run `coldbox ai refresh` after pulling updates or installing new modules to keep agent configs current.

**Custom Guidelines** — use `.agents/guidelines/custom/` for business domain concepts, third-party service integrations, and team conventions that the AI should always know about.

**Custom Skills** — use `.agents/skills/` for project-specific workflows, deployment procedures, and domain patterns not covered by [skills.boxlang.io](https://skills.boxlang.io).
