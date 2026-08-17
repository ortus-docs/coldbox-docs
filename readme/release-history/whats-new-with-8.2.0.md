---
description: August 17, 2026
---

# What's New With 8.2.0

ColdBox 8.2.0 is a feature release focused on request-level composition and control: route-scoped middleware, standards-based HTTP caching, first-class Server-Sent Events, and conversational context for AI routing — plus a security hardening fix, a handful of correctness fixes, and hot-path performance work in the Renderer and routing layers.

## Major Highlights

### 🧵 Route-Scoped Middleware — `.middleware()`

Routes can now carry their own middleware chain instead of relying solely on app-wide interceptors. `.middleware()` attaches a closure, a WireBox ID, or any object with a method named after the interception point (`preProcess` by default, or `postProcess`) to a single route — reusing the same dispatch mechanism ColdBox interceptors already use, just scoped to one route.

```javascript
route( "/admin/:action" )
    .middleware( function( event, rc, prc ){
        if ( !auth.isLoggedIn() ) {
            event.relocate( "login" );
            return true; // short-circuits the rest of this route's middleware
        }
    } )
    .toHandler( "admin" );
```

Two companion features round this out:

* **`middlewareGroup( name, [ ...targets ] )`** — register a named, reusable bundle once, then reference it by name from `.middleware()` or a `group()`'s `middleware` option, instead of repeating the same target list everywhere.
* **`.withoutMiddleware( target )`** — opt a single route out of middleware it would otherwise inherit, by target name, by the group name it expanded from, or `"*"` for everything.

```javascript
middlewareGroup( "api", [ "RequireApiKey", "RateLimiter" ] );

group( { pattern : "/api", middleware : [ "api" ] }, function(){
    route( "/users" ).toHandler( "users" );                              // runs "api"
    route( "/health" ).withoutMiddleware( "api" ).toHandler( "health" ); // opts out
} );
```

See [Route Middleware](../../the-basics/routing/routing-dsl/middleware.md) and [Middleware Groups & Exclusions](../../the-basics/routing/routing-dsl/middleware-groups.md).

### 🗄️ HTTP Caching Primitives — ETag, Last-Modified, Cache-Control

Standards-based conditional-GET support, usable from any handler:

```javascript
function show( event, rc, prc ){
    prc.product = productService.get( rc.id );

    if ( event.etag( prc.product.getHash() ) ) {
        return; // 304 already sent - nothing left to do
    }

    event.setView( "products/show" );
}
```

* **`event.etag()`** / **`event.lastModified()`** — set the corresponding header and short-circuit with a `304 Not Modified` on a match. Never short-circuits an unsafe HTTP method.
* **`event.cacheControl()`** — build a `Cache-Control` header from a directives struct.
* **`Response.withETag()`** / **`Response.withCacheControl()`** — the same primitives, as fluent methods on the REST `Response` object.
* An already `cache="true"` event handler action can opt into an **automatically computed** `ETag`/`Last-Modified` with new `etag`/`etagWeak`/`lastModified`/`cacheControl` annotations, piggybacking on event caching with no extra per-request work.

See [HTTP Caching](../../digging-deeper/http-caching.md).

### 📡 First-Class Server-Sent Events (BoxLang)

Streaming is now a first-class concern instead of something bolted onto AI routing. `event.sse()` takes over the response and hands your callback an `SSEEmitter` with `send()`, `sendView()`, `sendData()`, `sendError()`, keep-alives, and graceful disconnect handling:

```javascript
function ticker( event, rc, prc ){
    event.sse( ( emitter ) => {
        while ( emitter.isOpen() ) {
            emitter.send( { "ts" : now() }, "tick" );
            sleep( 1000 );
        }
    } );
}
```

For routes that always stream, `Router.toSSE()` mirrors `toResponse()`. Three new interception points (`preSSEConnection`, `postSSEConnection`, `onSSEError`) let you reject a connection before it opens, observe stream completion, or react to mid-stream errors, and a new `this.sse` settings block controls keep-alive interval, reconnect hints, and CORS defaults.

See [Server-Sent Events](../../the-basics/event-handlers/server-sent-events.md) and [Streaming Routes (SSE)](../../the-basics/routing/routing-dsl/sse-routes.md).

### 🤖 AI Routing Gets Conversational Context

`toAi()`'s `invoke`, `stream`, and `batch` sub-routes now resolve `userId`, `conversationId`, and `threadId` from the request body and thread them through to the runnable via `options`:

* **`userId`** defaults to the framework's own session/request tracking identifier when not supplied
* **`conversationId`** is passed through only if supplied - no default is invented
* **`threadId`** is generated if not supplied, and is **always** echoed back - in the JSON response, an `X-Thread-Id` header, and a leading `event: thread` SSE frame on `/stream` (since browser `EventSource` clients can't read response headers)

```javascript
// POST /api/chat/invoke  { "input": "hi", "threadId": "t-123" }
// → runnable.run( "hi", {}, { userId: "<session id>", threadId: "t-123" } )
// → { "output": ..., "success": true, "threadId": "t-123" }
```

This release also corrects the `toAi()` reference documentation, which had drifted from the actual `run()`/`stream()`-based `IAiRunnable` interface and request/response shapes. See [AI Routing](../../the-basics/routing/routing-dsl/ai-routing.md).

### 🔒 Hardened HTTP Method Spoofing

The `_method` form-field override (`GET`/`POST` browsers use to fake `PUT`/`PATCH`/`DELETE`) is now only honored when the *original* transport-level request is a `POST`. Previously, a plain `GET` request carrying `?_method=DELETE` was silently treated as a `DELETE` — enabling CSRF-style attacks via a link, an `<img>` tag, a crawler, or a browser prefetch. A new `event.getOriginalHTTPMethod()` returns the raw, un-spoofed verb when you need it. See [HTTP Method Spoofing](../../the-basics/routing/http-method-spoofing.md).

### ⚡ Renderer & Routing Performance

* A new `viewDiscoveryCaching` setting (on by default, independent of `viewCaching`) caches the filesystem work that locates view/layout files, benefiting every render regardless of whether view *output* caching is enabled. See [View Discovery Caching](../../the-basics/layouts-and-views/views/view-caching.md#view-discovery-caching).
* Several hot-path costs eliminated in `HandlerService`, `RoutingService`, and `Router` — a per-request settings lookup that should have been cached at configuration time, a redundant filesystem check in view dispatch detection, and route response placeholders now pre-parsed once at registration instead of re-parsed by regex on every matching request.
* The interceptor chain now reports short-circuiting: `announce()` returns `true` on the synchronous path when an interceptor short-circuited the chain, `false` otherwise - previously this was undetectable. See [Announcing Interceptions](../../the-basics/interceptors/custom-events/announcing-interceptions.md#detecting-a-short-circuit).

## Release Notes

{% tabs %}
{% tab title="ColdBox" %}
### New Features

[COLDBOX-1415](https://ortussolutions.atlassian.net/browse/COLDBOX-1415) HTTP caching primitives - ETag, Last-Modified, Cache-Control

[COLDBOX-1416](https://ortussolutions.atlassian.net/browse/COLDBOX-1416) Route-scoped middleware via existing interceptor points

[COLDBOX-1417](https://ortussolutions.atlassian.net/browse/COLDBOX-1417) Conversational context (userId/conversationId/threadId) on `toAi()` routes

First-class Server-Sent Events streaming - `event.sse()`, `SSEEmitter`, `Router.toSSE()`, interception points, `this.sse` settings ([#676](https://github.com/ColdBox/coldbox-platform/pull/676))

### Improvements

[COLDBOX-1406](https://ortussolutions.atlassian.net/browse/COLDBOX-1406) HTTP method spoofing restricted to `POST` requests only; new `getOriginalHTTPMethod()`

New `viewDiscoveryCaching` setting plus Renderer hot-path caching for view/layout discovery ([#664](https://github.com/ColdBox/coldbox-platform/pull/664))

Hot-path performance optimizations in `HandlerService`, `RoutingService`, and `Router` ([#665](https://github.com/ColdBox/coldbox-platform/pull/665))

Interceptor chain now reports and honors short-circuiting via `announce()`'s return value; new `getRouteDefinitionKeys()` route-table introspection helper ([#677](https://github.com/ColdBox/coldbox-platform/pull/677))

### Bugs

[COLDBOX-1407](https://ortussolutions.atlassian.net/browse/COLDBOX-1407) `url.results` collision breaking `cache.getOrSet()` on any page loaded with `?results=...`

[COLDBOX-1411](https://ortussolutions.atlassian.net/browse/COLDBOX-1411) `this.EVENT_CACHE_SUFFIX` closures were evaluated once and frozen instead of per-request, letting different requests share a cache key

`DataMarshaller` component made thread-safe ([#672](https://github.com/ColdBox/coldbox-platform/pull/672))

Fixed a startup typo in `Bootstrap.cfc` ([#667](https://github.com/ColdBox/coldbox-platform/pull/667))
{% endtab %}
{% endtabs %}
