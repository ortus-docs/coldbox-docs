# Redirects Required (GitBook Dashboard)

The docs restructure moved or removed the pages below. Old URLs must redirect in the GitBook dashboard (**Space settings → Content → Redirects**) before this ships, so external links (whats-new pages, blog posts, ForgeBox) don't 404.

## Deleted sections

| Old path | Redirect to |
| --- | --- |
| `/architecture-concepts/how-coldbox-works` | `/getting-started/request-lifecycle` |
| `/digging-deeper/recipes` | `/digging-deeper/rest-handler` |
| `/digging-deeper/recipes/building-rest-apis` | `/digging-deeper/rest-handler` |
| `/digging-deeper/recipes/coldbox-exception-handling` | `/reference/configuration-directives/coldbox` |
| `/digging-deeper/recipes/debugging-coldbox-apps` | `/the-basics/testing-quick-start/tips-and-tricks` |
| `/digging-deeper/recipes/clearing-the-view-cache` | `/the-basics/layouts-and-views/views/view-caching` |
| `/digging-deeper/recipes/building-a-simple-basic-http-authentication-interceptor` | `/ecosystem/security` |
| `/digging-deeper/promises-async-programming/scheduled-tasks` | `/digging-deeper/scheduled-tasks` |

## Configuration directives (15 pages → 5)

| Old path | Redirect to |
| --- | --- |
| `/getting-started/configuration/coldbox.cfc/configuration-directives` | `/reference/configuration-directives` |
| `…/configuration-directives/coldbox` | `/reference/configuration-directives/coldbox` |
| `…/configuration-directives/conventions` | `/reference/configuration-directives/coldbox#conventions` |
| `…/configuration-directives/environments` | `/reference/configuration-directives/coldbox#environments` |
| `…/configuration-directives/flash` | `/reference/configuration-directives/coldbox#flash` |
| `…/configuration-directives/interceptors` | `/reference/configuration-directives/coldbox#interceptors` |
| `…/configuration-directives/interceptorsettings` | `/reference/configuration-directives/coldbox#interceptors` |
| `…/configuration-directives/layouts` | `/reference/configuration-directives/coldbox#layouts` |
| `…/configuration-directives/layoutsettings` | `/reference/configuration-directives/coldbox#layouts` |
| `…/configuration-directives/settings` | `/reference/configuration-directives/coldbox#settings` |
| `…/configuration-directives/cachebox` | `/reference/configuration-directives/cachebox` |
| `…/configuration-directives/logbox` | `/reference/configuration-directives/logbox` |
| `…/configuration-directives/wirebox` | `/reference/configuration-directives/wirebox` |
| `…/configuration-directives/modules` | `/reference/configuration-directives/modules-and-settings` |
| `…/configuration-directives/modulesettings` | `/reference/configuration-directives/modules-and-settings#module-settings` |
| `/getting-started/configuration/coldbox.cfc/system-settings-java-properties-and-environment-variables` | `/reference/system-settings` |

## Redirect stubs left in place

These files still exist at their old paths and link onward, so they don't strictly need dashboard redirects:

- `/the-basics/modules/core-modules` → links to `/ecosystem`

## Nav retitles (no URL change)

- "For Newbies" → **Tutorials** (paths unchanged)
- "ColdBox.cfc" → **The ColdBox Class** (path unchanged)
- Testing promoted to a top-level section (paths unchanged)
- AI promoted to a top-level section (paths unchanged, still under `digging-deeper/ai/`)
