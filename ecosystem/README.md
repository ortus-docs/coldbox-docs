---
description: The ColdBox ecosystem catalog — every actively maintained module, grouped by concern, with install commands and links to each module's own documentation.
icon: cubes
---

# Modules Catalog

ColdBox is deliberately lean: capabilities beyond the core framework ship as **modules** installable from [ForgeBox](https://forgebox.io). This catalog lists the actively maintained modules from the [coldbox-modules](https://github.com/coldbox-modules) organization, grouped by concern.

Each entry links to the module's own documentation — the guides in this section cover the most widely used ones. Install any module with CommandBox:

```bash
box install <slug>
```

## Security

| Module | What it does | Docs |
| --- | --- | --- |
| `cbsecurity` | Rule-driven security engine (sessions, JWT) | [book](https://coldbox-security.ortusbooks.com) |
| `cbauth` | Authentication service backing cbsecurity | [ForgeBox](https://forgebox.io/view/cbauth) |
| `cbcsrf` | CSRF token generation and verification | [ForgeBox](https://forgebox.io/view/cbcsrf) |
| `cbsecurity-passkeys` | WebAuthn/passkey authentication | [ForgeBox](https://forgebox.io/view/cbsecurity-passkeys) |
| `cbSSO` | Single sign-on across ColdBox apps | [ForgeBox](https://forgebox.io/view/cbSSO) |
| `bcrypt` | BCrypt password hashing | [ForgeBox](https://forgebox.io/view/bcrypt) |
| `cbantisamy` | OWASP AntiSamy XSS cleanup | [ForgeBox](https://forgebox.io/view/cbantisamy) |

→ Guide: [Security](security.md)

## Data & Persistence

| Module | What it does | Docs |
| --- | --- | --- |
| `cborm` | Hibernate ORM extensions (services, criteria, ActiveEntity) | [book](https://cborm.ortusbooks.com) |
| `quick` | Entity-based ORM engine (no Hibernate required) | [book](https://quick.ortusbooks.com) |
| `qb` | Fluent query builder | [book](https://qb.ortusbooks.com) |
| `cfmigrations` | Database migrations | [book](https://cfmigrations.ortusbooks.com) |
| `mementifier` | Object-to-struct serialization | [book](https://mementifier.ortusbooks.com) |
| `cbpaginator` | Pagination for queries and collections | [ForgeBox](https://forgebox.io/view/cbpaginator) |

→ Guides: [Data & Persistence](data-and-persistence.md), [Serialization](serialization.md)

## Web & APIs

| Module | What it does | Docs |
| --- | --- | --- |
| `hyper` | Fluent HTTP client | [book](https://hyper.ortusbooks.com) |
| `cbswagger` | OpenAPI docs generated from your routes | [ForgeBox](https://forgebox.io/view/cbSwagger) |
| `cbfeeds` | Consume and produce RSS/ATOM feeds | [ForgeBox](https://forgebox.io/view/cbfeeds) |
| `cbwire` | Reactive, Livewire-style components | [book](https://cbwire.ortusbooks.com) |
| `SocketBox` | WebSocket server integration | [ForgeBox](https://forgebox.io/view/SocketBox) |
| `cbstreams` | Functional stream operations | [ForgeBox](https://forgebox.io/view/cbstreams) |

→ Guide: [HTTP Client](http-client.md), [API Documentation](api-documentation.md)

## Messaging & Background Work

| Module | What it does | Docs |
| --- | --- | --- |
| `cbq` | Queue-based job dispatching | [book](https://cbq.ortusbooks.com) |
| `cbmailservices` | Protocol-based mail sending (CFMail, file, Postmark, SendGrid) | [book](https://cbmailservices.ortusbooks.com) |
| `cbmessagebox` | Flash-scoped, skinnable user messages | [ForgeBox](https://forgebox.io/view/cbmessagebox) |

→ Guides: [Queues](queues.md), [Mail](mail.md)

## File Storage

| Module | What it does | Docs |
| --- | --- | --- |
| `cbfs` | Unified filesystem abstraction (local, S3, R2) | [book](https://cbfs.ortusbooks.com) |
| `s3sdk` | Amazon S3 SDK | [ForgeBox](https://forgebox.io/view/s3sdk) |
| `r2sdk` | Cloudflare R2 client | [ForgeBox](https://forgebox.io/view/r2sdk) |
| `cbfs-r2` | R2 storage provider for cbfs | [ForgeBox](https://forgebox.io/view/cbfs-r2) |

→ Guide: [File Storage](file-storage.md)

## Validation & Rules

| Module | What it does | Docs |
| --- | --- | --- |
| `cbvalidation` | Server-side object and form validation | [book](https://cbvalidation.ortusbooks.com) |
| `rulebox` | Natural-language business rules engine | [ForgeBox](https://forgebox.io/view/rulebox) |

→ Guide: [Validation](validation.md)

## Developer Tools

| Module | What it does | Docs |
| --- | --- | --- |
| `cbdebugger` | Request profiler and debug panel | [ForgeBox](https://forgebox.io/view/cbdebugger) |
| `route-visualizer` | Visual map of your route table | [ForgeBox](https://forgebox.io/view/route-visualizer) |
| `cbMockData` | Fake data generation for tests | [ForgeBox](https://forgebox.io/view/cbMockData) |
| `cbPlaywright` | Browser testing via Playwright | [ForgeBox](https://forgebox.io/view/cbPlaywright) |
| `cbMCP` | Expose your running app as an MCP server | [ForgeBox](https://forgebox.io/view/cbMCP) |

## Search & Observability

| Module | What it does | Docs |
| --- | --- | --- |
| `cbelasticsearch` | Elasticsearch integration | [ForgeBox](https://forgebox.io/view/cbelasticsearch) |
| `cbmeilisearch` | Meilisearch integration | [ForgeBox](https://forgebox.io/view/cbmeilisearch) |
| `sentry` | Sentry error reporting | [ForgeBox](https://forgebox.io/view/sentry) |
| `cbotel` | OpenTelemetry tracing | [ForgeBox](https://forgebox.io/view/cbotel) |
| `logstash` | Logstash appender for LogBox | [ForgeBox](https://forgebox.io/view/logstash) |

## Utilities

| Module | What it does | Docs |
| --- | --- | --- |
| `cbi18n` | Localization and resource bundles | [ForgeBox](https://forgebox.io/view/cbi18n) |
| `cbstorages` | Facades over native persistence scopes | [ForgeBox](https://forgebox.io/view/cbstorages) |
| `cbcommons` | Date, file, JVM, query, and zip helpers | [ForgeBox](https://forgebox.io/view/cbcommons) |
| `cbmarkdown` | Markdown rendering | [ForgeBox](https://forgebox.io/view/cbmarkdown) |
| `cbemoji` | Emoji parsing and rendering | [ForgeBox](https://forgebox.io/view/cbemoji) |
| `cbjavaloader` | Dynamic Java classloading (CFML engines) | [ForgeBox](https://forgebox.io/view/cbjavaloader) |
| `cbproxies` | Generate proxies to ColdBox services | [ForgeBox](https://forgebox.io/view/cbproxies) |
| `vite-helpers` | Vite asset helpers for views | [ForgeBox](https://forgebox.io/view/vite-helpers) |

→ Guide: [i18n](i18n.md)

## Legacy Modules

These modules are no longer actively developed. They remain on ForgeBox for existing applications but are not recommended for new projects.

| Module | Note |
| --- | --- |
| `cbopenai` | Superseded by [BoxLang AI (`bx-ai`)](../digging-deeper/ai/boxlang-ai.md) |
| `cbRedis` | Dormant since 2016 |
| `relax` | REST API tooling, dormant |
| `cboptional` | Java Optional helpers, dormant |
| `cbyaml` | YAML parsing, dormant |
| `swagger-sdk` | Superseded by `cbswagger` |
| `cbioc` | Third-party DI integration — WireBox covers this |
| `cbsoap` | SOAP helper, dormant |
| `cffractal` | Serialization, superseded by `mementifier` |
| `coldbox-asset-bag` | Superseded by Vite-based asset pipelines |
