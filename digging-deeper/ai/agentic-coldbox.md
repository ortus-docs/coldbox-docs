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
3. **Agents** - AI assistant configurations (Claude, Copilot, Cursor, Codex, Gemini, Kilo Code, OpenCode, Pi)
4. **MCP Servers** - Context protocol servers tracked in `.mcp.json` for live documentation and application introspection

{% hint style="info" %}
Make sure you are on the latest `coldbox-cli` in your CommandBox installation before getting started.
{% endhint %}

### Key Features

* **Dual-Language Support** - First-class support for BoxLang and CFML with automatic detection
* **Multi-Agent Ecosystem** - Works with Claude, GitHub Copilot, Cursor, Codex, Gemini, Kilo Code, OpenCode, and Pi
* **3 Core Guidelines** - ColdBox, BoxLang, and CFML guidelines ship on-disk; agent files stay lean (~250 lines)
* **200+ Skills Registry** - All skills sourced from [skills.boxlang.io](https://skills.boxlang.io), the centralized Ortus ecosystem skill repository
* **30+ MCP Servers** - Built-in documentation servers auto-matched to installed modules
* **Live App Introspection** - [`cbMCP`](coldbox-mcp-server.md) (BoxLang only) lets AI agents query your running application in real time
* **Override System** - Customize core guidelines and skills at the project level

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
| **Kilo Code**      | `AGENTS.md` (shared) |
| **OpenCode**       | `AGENTS.md` (shared) |
| **Pi**             | `AGENTS.md` (shared) |

{% hint style="info" %}
After installation, add your project context to the generated agent file — business domain, key services, authentication approach, and API endpoints. This gives AI assistants the application-specific knowledge they need.
{% endhint %}

### Agent File Conflict Resolution

If `coldbox ai install` or `coldbox ai refresh` detects existing agent configuration files (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`, `.cursorrules`) that were **not created by ColdBox CLI**, you'll be prompted to choose how to handle each conflict:

| Option | Behavior |
| ------ | -------- |
| **Overwrite** | Replace the entire file with ColdBox CLI content |
| **Merge** | Prepend the ColdBox CLI managed section at the top, preserving your custom content below |
| **Skip** | Leave the existing file untouched |

Use `--force` to automatically overwrite all conflicting files without prompting:

```bash
coldbox ai install --force
coldbox ai refresh --force
```

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

Custom guidelines live in `.agents/guidelines/custom/` and are always available to the AI without needing to be explicitly requested. Use them to document your business domain, third-party integrations, architecture decisions, and team conventions:

```bash
# Create a custom guideline
touch .agents/guidelines/custom/payment-processing.md
```

Example guideline structure:

```markdown
# Payment Processing

This application uses Stripe with a custom abstraction layer in `models/payments/`.

## Architecture

- `models/payments/` - Payment domain models
- `services/PaymentService.cfc` - Main payment service

## Key Conventions

1. Always validate amounts before charging
2. Log all payment attempts to the `payment-audit` logger
3. Use idempotency keys for charge retries
4. Webhook events are handled in `handlers/webhooks/Stripe.cfc`
```

### Overriding Guidelines

```bash
# Override a core guideline with your own version
coldbox ai guidelines install coldbox --override
```

***

## AI Skills

[skills.boxlang.io](https://skills.boxlang.io) is the **centralized skill repository for the entire Ortus ecosystem** — ColdBox, BoxLang, TestBox, CommandBox, and all major modules. With 200+ skills available, it is the single source of truth for implementation cookbooks. Skills are installed per-project and loaded on-demand by AI agents when the task matches.

```bash
# Install a skill
coldbox ai skills install creating-handlers

# List installed skills
coldbox ai skills list
coldbox ai skills list --verbose
coldbox ai skills list --json            # Machine-readable JSON output

# Check for registry updates
coldbox ai skills list --outdated

# Update skills from registry
coldbox ai skills update                 # Re-download all installed skills
coldbox ai skills update creating-handlers  # Re-download a single skill
```

### Agent Skill-Directory Symlinks

Each supported AI agent has a dedicated skills directory where it expects to find skills:

| Agent | Skills Directory |
| ----- | ---------------- |
| **Claude** | `.claude/skills/` |
| **Copilot** | `.github/instructions/` |
| **Cursor** | `.cursor/rules/` |
| **Kilo Code** | `.kilo/skills/` |
| **Pi** | `.pi/skills/` |
| **Codex, Gemini, OpenCode** | Use `.agents/skills/` directly |

When a skill is installed, the CLI creates the skill at `.agents/skills/{name}/` and automatically creates a **relative directory symlink** inside each active agent's dedicated skills directory (e.g., `.claude/skills/{name}` → `../../.agents/skills/{name}`). This lets every agent discover skills through its own expected path without duplicating content.

Symlinks are automatically removed when a skill is removed (`coldbox ai skills remove`) or pruned during refresh.

### Custom Skills

Create project-specific skills for workflows not covered by the registry. Each skill lives in its own folder with a `SKILL.md` file:

```bash
mkdir -p .agents/skills/deploying-to-kubernetes
touch .agents/skills/deploying-to-kubernetes/SKILL.md
```

A `SKILL.md` should include a brief description of when to use the skill, step-by-step implementation instructions, and working code examples:

```markdown
---
name: deploying-to-kubernetes
description: Deploy ColdBox applications to our Kubernetes cluster using Helm
---

# Deploying to Kubernetes

## When to use this skill

Use this skill when deploying any ColdBox application to our production cluster.

## Steps

1. Build the Docker image: `docker build -t myapp:latest .`
2. Update `kubernetes/values.yaml` with the new image tag
3. Deploy: `helm upgrade --install myapp ./kubernetes/chart -f kubernetes/values.yaml`

## Rollback

```bash
helm rollback myapp -n production
```
```

***

## AI Agents

ColdBox AI Integration supports **8 major AI agents** with automatic configuration generation:

| Agent              | Config File          | Description                         |
| ------------------ | -------------------- | ----------------------------------- |
| **Claude**         | `CLAUDE.md`          | Claude Desktop and Claude Code      |
| **GitHub Copilot** | `AGENTS.md` (shared) | VS Code Copilot integration         |
| **Cursor**         | `.cursorrules`       | Cursor IDE rules                    |
| **Codex**          | `AGENTS.md` (shared) | Codex AI assistant                  |
| **Gemini**         | `GEMINI.md`          | Gemini CLI integration              |
| **Kilo Code**      | `AGENTS.md` (shared) | Kilo Code AI assistant              |
| **OpenCode**       | `AGENTS.md` (shared) | OpenCode assistant                  |
| **Pi**             | `AGENTS.md` (shared) | Pi AI assistant                     |

> 📁 **Shared Config**: GitHub Copilot, Codex, Kilo Code, OpenCode, and Pi all share the `AGENTS.md` file, following the [Agents.md standard](https://github.blog/changelog/2025-08-28-copilot-coding-agent-now-supports-agents-md-custom-instructions/).

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

{% hint style="warning" %}
`cbMCP` is available for **BoxLang applications only**.
{% endhint %}

The [`cbMCP`](coldbox-mcp-server.md) module turns your **running BoxLang ColdBox application** into an MCP server, giving AI agents real-time access to routes, handlers, WireBox mappings, and more. See the [ColdBox MCP Server documentation](coldbox-mcp-server.md) for the full tool reference, custom tool development, and AI client setup.

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

### VSCode Copilot MCP Mirroring

When **Copilot** is a configured agent, MCP server configuration is automatically mirrored to `.vscode/mcp.json` using the VSCode-specific schema (`"servers"` + `"inputs": []`). This ensures GitHub Copilot in VS Code can discover all registered MCP servers without additional configuration. The `.vscode/mcp.json` file is written alongside the root `.mcp.json` whenever MCP configuration is regenerated (install, refresh, MCP add/remove).

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
coldbox ai refresh                  # Sync with installed modules, auto-recover missing skills, regenerate agent configs
```

During refresh, the CLI automatically:
- Discovers and installs new module guidelines and skills
- **Auto-recovers** any missing core skills (boxlang, coldbox, testbox, commandbox)
- **Auto-installs** skills for newly added `box.json` module dependencies
- Detects and registers MCP documentation servers from installed modules
- Regenerates agent configuration files and skill symlinks
- Respects the `manifest.excludes[]` list — removed skills won't be reinstalled

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
coldbox ai skills list --json                 # Machine-readable JSON
coldbox ai skills list --outdated             # Check for registry updates
coldbox ai skills install creating-handlers   # Install specific
coldbox ai skills update                      # Re-download all registry skills
coldbox ai skills update creating-handlers    # Re-download a single skill
coldbox ai skills uninstall creating-handlers # Remove skill (tracked to prevent auto-reinstall)

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

**Stay Synced** — run `coldbox ai refresh` after pulling updates or installing new modules to keep agent configs current. Removed skills are tracked in `manifest.excludes[]` and won't be auto-reinstalled.

**VSCode Users** — when Copilot is configured as an agent, `.vscode/mcp.json` is auto-generated alongside `.mcp.json` so Copilot can discover MCP servers. Commit both files to share MCP configuration with your team.

**Custom Guidelines** — use `.agents/guidelines/custom/` for business domain concepts, third-party service integrations, and team conventions that the AI should always know about.

**Custom Skills** — use `.agents/skills/` for project-specific workflows, deployment procedures, and domain patterns not covered by [skills.boxlang.io](https://skills.boxlang.io).
