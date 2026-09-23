---
description: AI guidelines and 200+ skills from skills.boxlang.io — on-demand knowledge for your coding agents.
icon: book-open
---

# Skills & Guidelines

ColdBox's AI integration splits agent knowledge into two complementary pieces: **guidelines** (always-relevant conventions) and **skills** (on-demand task cookbooks). Both live in your project's `.agents/` directory.

## Guidelines

Guidelines teach agents framework conventions and architectural patterns. Three core guidelines ship with the CLI:

| Guideline | Covers |
| --- | --- |
| `coldbox` | ColdBox architecture, handlers, routing, DI, conventions |
| `boxlang` | BoxLang syntax, classes, lambdas, BIFs |
| `cfml` | CFML fundamentals for Adobe/Lucee projects |

Module-provided guidelines are auto-discovered: install `qb` or `cbsecurity` and their guidelines appear in `.agents/guidelines/modules/` after `coldbox ai refresh`.

```bash
coldbox ai guidelines list --verbose    # What's installed
coldbox ai guidelines add qb cbsecurity # Add specific guidelines
coldbox ai guidelines override coldbox  # Customize a core guideline locally
```

## Skills

Skills are step-by-step implementation cookbooks sourced from the central registry at **[skills.boxlang.io](https://skills.boxlang.io)** — 200+ skills covering ColdBox, BoxLang, CommandBox, TestBox, and the module ecosystem.

Unlike guidelines, skills are **inventoried, not loaded**: your agent sees a catalog of skill names and descriptions, and reads a skill's `SKILL.md` only when the task calls for it. That keeps base context lean while deep knowledge stays one `read_file` away.

```bash
coldbox ai skills list --verbose   # Browse installed skills
coldbox ai skills find <term>      # Search the registry
coldbox ai skills install <slug>   # Install from the registry
coldbox ai skills refresh          # Sync with installed modules
```

Examples of what agents can pull on demand: building CRUD handlers, REST APIs, integration tests, scheduled tasks, migrations, validation constraints, and BoxLang runtime deployment recipes.

## For Module Authors

Bundle AI resources in your own module by adding `.agents/guidelines/` and `.agents/skills/` to it — the CLI auto-discovers them for every consumer of your module. Scaffold them with:

```bash
coldbox create module myModule --ai
```

Full authoring workflow: [Agentic ColdBox](../../digging-deeper/ai/agentic-coldbox.md).

## See Also

- [ColdBox CLI AI Setup](coldbox-cli-ai-setup.md)
- [BoxLang IDE](boxlang-ide.md)
