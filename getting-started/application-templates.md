---
description: The best way to get started with ColdBox
icon: bulldozer
---

# Application Templates

The best way to get started with ColdBox is with our application templates at [coldbox-templates](https://github.com/coldbox-templates) — a curated collection of starter applications you scaffold with the `coldbox create app` command in the CLI:

{% embed url="https://github.com/coldbox-templates" %}
github.com/coldbox-templates
{% endembed %}

## CbGenesis — The Flagship Starter

**[CbGenesis](https://cbgenesis.coldbox.org)** is our production-ready reference application and the star starting point for any serious ColdBox project. It's a fully working app — not an empty skeleton — built on the Modern template layout with BoxLang:

- **Authentication & security** — session + JWT auth, role-based permissions, API tokens (cbsecurity + cbauth)
- **Database stack** — ORM (cborm), qb query builder, cfmigrations migrations, cbvalidation
- **Admin panel** — dark mode, Alpine.js, Bootstrap 5, Vite asset pipeline
- **Testing & CI** — TestBox suites and GitHub Actions ready to go

```bash
coldbox create app myApp skeleton=https://github.com/coldbox-templates/cbGenesis
```

Use CbGenesis to see how the [Ecosystem](../ecosystem/README.md) modules fit together in a real application.

## Starter Templates

| Template | Description |
| --- | --- |
| `boxlang` | **Recommended.** Native BoxLang app: secure non-root layout, Vite, `Build.bx`, Docker-ready |
| `flat` | Traditional CFML-style layout — everything in the webroot |
| `rest` | A base REST API using ColdBox |
| `rest-hmvc` | An HMVC REST API using modules |
| `supersimple` | Barebones conventions baby! |
| `boxlang-desktop` | Cross-platform desktop app via the [BoxLang Desktop runtime](../digging-deeper/desktop-applications.md) |
| `vite` | Vite-first frontend pipeline variant |

```bash
coldbox create app myApp                  # Default template
coldbox create app myApp skeleton=boxlang # Native BoxLang
coldbox create app myApp --ai             # Any template + AI integration
```

## CommandBox Integration

The `skeleton` argument accepts more than built-in names:

* A ForgeBox entry: `skeleton=cbtemplate-simple`
* A GitHub shortcut: `skeleton=github-username/repo`
* An HTTP/S URL to a zip: `skeleton=http://myapptemplates.com/template.zip`
* A local folder: `skeleton=/opt/shared/templates/my-template`
* A local zip: `skeleton=/opt/shared/templates/my-template.zip`

## See Also

- [ColdBox CLI](coldbox-cli.md) — full `create app` reference
- [Agentic Development](agentic-development/README.md) — the `--ai` flag
- [Desktop Applications](../digging-deeper/desktop-applications.md)
