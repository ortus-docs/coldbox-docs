---
description: >-
  Declare event caching and HTTP caching rules on a route instead of on the
  handler action.
---

# Route-Level Caching

`.withCache()` is a route-scoped alternative to handler `cache="true"` annotations, so caching can be declared where the URL is declared instead of buried on the handler action.

```javascript
route( "/products/:id" )
    .withCache( timeout : 30 )
    .toHandler( "products.show" );
```

## Arguments

`.withCache()` mirrors every existing handler-level cache annotation one-for-one, plus the [HTTP caching](../../../digging-deeper/http-caching.md) primitives:

| Argument                  | Mirrors handler annotation | Description                                    |
| -------------------------- | --------------------------- | ----------------------------------------------- |
| `cache`                    | `cache`                      | Boolean toggle, defaults to `true` when called |
| `cacheTimeout`              | `cacheTimeout`                | Minutes before the entry expires                |
| `cacheLastAccessTimeout`    | `cacheLastAccessTimeout`      | Idle-eviction timeout, in minutes               |
| `cacheProvider`             | `cacheProvider`                | Named CacheBox provider to store the entry in   |
| `cacheSuffix`               | `EVENT_CACHE_SUFFIX`           | A static string, or `function( event )`         |
| `cacheInclude`              | `cacheInclude`                 | RC keys to fold into the cache key hash         |
| `cacheExclude`              | `cacheExclude`                 | RC keys to leave out of the hash                |
| `cacheFilter`               | `cacheFilter`                  | A closure deciding whether to cache this request |
| `etag` / `etagWeak`         | n/a                             | Auto-computed `ETag` on a cache hit or store     |
| `lastModified`              | n/a                             | Auto-computed `Last-Modified` on a cache hit or store |
| `cacheControl`              | n/a                             | A `Cache-Control` directives struct              |

{% hint style="info" %}
`cacheSuffix` closures use the signature `function( event )`, distinct from the handler-level `EVENT_CACHE_SUFFIX`'s `function( eventHandlerBean, event )` - a route has no reflected handler-action metadata to hand it.
{% endhint %}

## Precedence

A route that calls `.withCache()` takes full precedence over that same event's handler-level cache annotations, for any request matching it. Routes that don't opt in fall through unchanged to the existing handler-annotation path - zero behavior change for apps that don't use it.

This means two different routes pointing at the same event can carry two different cache policies - something a handler annotation, keyed by event name alone, could never do:

```javascript
// Public catalog view: cache aggressively
route( "/catalog/:id" )
    .withCache( timeout : 60 )
    .toHandler( "products.show" );

// Admin preview of the same handler: never cache
route( "/admin/preview/:id" ).toHandler( "products.show" );
```

## Combining With HTTP Caching

`etag`, `etagWeak`, `lastModified`, and `cacheControl` piggyback on the same event-caching lookup, so a cached route can answer a conditional `GET` with a `304 Not Modified` before the handler ever runs again:

```javascript
route( "/products/:id" )
    .withCache( timeout : 30, etag : true, cacheControl : { "max-age" : 60 } )
    .toHandler( "products.show" );
```

See [HTTP Caching](../../../digging-deeper/http-caching.md) for the underlying `event.etag()`/`event.cacheControl()` primitives this builds on.

{% hint style="success" %}
**Next:** the underlying ETag/Last-Modified/Cache-Control primitives you can also call directly from a handler - see [HTTP Caching](../../../digging-deeper/http-caching.md).
{% endhint %}
