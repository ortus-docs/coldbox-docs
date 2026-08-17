# Terminators At a Glance

This page used to hold every route terminator in one long document. It's now split into focused pages - use this as an index:

| Terminator                                    | What it does                                    |
| ---------------------------------------------- | ------------------------------------------------ |
| [Sending Requests Somewhere](targets.md)       | `to()`, `toHandler()`, `toView()`, `toResponse()` |
| [Redirecting Routes](redirects.md)             | `toRedirect()`, including dynamic redirects       |
| [Resourceful Routes](resourceful-routes.md)    | `resources()`, `apiResources()`                   |
| [Route Middleware](middleware.md)              | `.middleware()`                                   |
| [Middleware Groups & Exclusions](middleware-groups.md) | `middlewareGroup()`, `.withoutMiddleware()` |
| [Subdomain Routing](subdomain-routing.md)      | `withDomain()`                                    |
| [Routing Conditions](routing-conditions.md)    | `withCondition()`                                 |
| [Adding Data to a Route](adding-data-to-a-route.md) | `rc()`, `prc()`, `rcAppend()`, `prcAppend()` |
| [Streaming Routes (SSE)](sse-routes.md)        | `toSSE()`                                         |
| [AI & MCP Routing](ai-routing.md)              | `toAi()`, `toMCP()`                               |

See the [Routing DSL overview](README.md) for the full method reference across initiators, modifiers, and terminators.
