---
description: BoxLang IDE — VS Code tooling for BoxLang and ColdBox development with language intelligence and AI integration.
icon: code
---

# BoxLang IDE

The **BoxLang IDE** is the official VS Code extension for BoxLang and ColdBox development: syntax highlighting, language intelligence, debugging, and AI-assisted coding in one package.

📖 **Full documentation:** [boxlang.ortusbooks.com/getting-started/ide-tooling/boxlang-ide](https://boxlang.ortusbooks.com/getting-started/ide-tooling/boxlang-ide)

## Install

Search for **BoxLang** in the VS Code marketplace, or install from the command line:

```bash
code --install-extension ortus-solutions.vscode-boxlang
```

The extension pack includes the BoxLang language server (LSP), so you get completions, hover docs, diagnostics, and navigation for `.bx`, `.bxm`, `.bxs`, `.cfc`, and `.cfm` files.

## Why It Matters for ColdBox

- **BoxLang-first development** — first-class support for the language ColdBox is optimized for
- **AI-ready** — pairs with GitHub Copilot and the [CLI's AI integration](coldbox-cli-ai-setup.md), so your assistant sees both the codebase and ColdBox conventions
- **Debugging** — breakpoints and step-through for BoxLang web and CLI apps
- **Templates & tooling** — scaffolding and CommandBox integration from the editor

## Recommended Setup

1. Install the BoxLang IDE extension
2. Install the ColdBox CLI: `box install coldbox-cli`
3. Run `coldbox ai install` in your project
4. Enable your AI agent of choice (Copilot, Claude Code, Cursor…)

Your editor now has language intelligence *and* project-aware AI.

## See Also

- [Agentic Development](README.md) — the full agent toolkit
- [ColdBox CLI](../coldbox-cli.md)
