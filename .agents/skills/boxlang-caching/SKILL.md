---
name: boxlang-caching
description: "Use this skill when implementing caching in BoxLang applications: cache providers, cachePut/cacheGet BIFs, output caching, cache regions, distributed caching with Redis or Couchbase, TTL policies, and distributed locking."
---

# BoxLang Caching

## Overview

BoxLang provides a flexible, pluggable caching system managed by `CacheService`.
Multiple named cache regions can coexist, each backed by a different provider.
The default in-memory cache is available immediately; Redis, Couchbase, JDBC, and
filesystem caches are available via modules.

## Cache Configuration

### In `boxlang.json`

```json
{
    "caches": {
        "default": {
            "provider": "BoxCacheProvider",
            "properties": {
                "maxObjects": 1000,
                "defaultTimeout": 60,
                "defaultLastAccessTimeout": 30,
                "reapFrequency": 5,
                "evictionPolicy": "LRU"
            }
        },
        "sessions": {
            "provider": "BoxCacheProvider",
            "properties": {
                "maxObjects": 5000,
                "defaultTimeout": 120
            }
        },
        "queries": {
            "provider": "jdbc",
            "properties": {
                "datasource": "mainDB",
                "table": "cache_entries"
            }
        },
        "files": {
            "provider": "filesystem",
            "properties": {
                "directory": "/tmp/bxcache"
            }
        }
    }
}
```

## Basic Cache Operations

```boxlang
// Store a value (default cache, 60-minute TTL)
cachePut( "user_#userId#", userStruct )

// Store with explicit TTL (timespan)
cachePut( "user_#userId#", userStruct, createTimeSpan( 0, 1, 0, 0 ) )  // 1 hour

// Store with TTL and idle timeout
cachePut(
    "user_#userId#",
    userStruct,
    createTimeSpan( 0, 2, 0, 0 ),   // absolute TTL: 2 hours
    createTimeSpan( 0, 0, 30, 0 )   // idle timeout: 30 minutes
)

// Retrieve
var user = cacheGet( "user_#userId#" )
if ( isNull( user ) ) {
    user = userService.findById( userId )
    cachePut( "user_#userId#", user, createTimeSpan( 0, 1, 0, 0 ) )
}

// Check existence without fetching
if ( cacheKeyExists( "user_#userId#" ) ) {
    // ...
}

// Remove
cacheRemove( "user_#userId#" )

// Remove multiple
cacheRemove( ["user_1", "user_2", "user_3"] )

// Clear all entries in default cache
cacheClear()
```

## Named Cache Regions

```boxlang
// Use a specific named cache
cachePut( "product_#id#", product, createTimeSpan(0,4,0,0), "", "products" )
var product = cacheGet( "product_#id#", false, "products" )

// Check in named cache
if ( cacheKeyExists( "product_#id#", "products" ) ) { ... }

// Clear a specific cache region
cacheClear( "products" )

// Get cache statistics
var stats = cacheGetStats( "products" )
writeOutput( "Hits: #stats.hits#, Misses: #stats.misses#, Size: #stats.size#" )

// List all cache IDs
var keys = cacheGetAllIds( "products" )
```

## Cache-Aside Pattern

```boxlang
// Reusable cache-aside helper
function cacheOrLoad(
    required string key,
    required function loader,
    any ttl = createTimeSpan(0,1,0,0),
    string cacheName = "default"
) {
    var cached = cacheGet( arguments.key, false, arguments.cacheName )
    if ( !isNull( cached ) ) {
        return cached
    }
    var fresh = arguments.loader()
    cachePut( arguments.key, fresh, arguments.ttl, "", arguments.cacheName )
    return fresh
}

// Usage
var config = cacheOrLoad(
    key    = "app_config",
    loader = () -> configService.loadFromDB(),
    ttl    = createTimeSpan(0,0,15,0)  // 15 minutes
)
```

## Output Caching

Cache rendered HTML output to avoid re-executing expensive templates:

```boxlang
// Cache entire block output for 10 minutes
bx:cache timespan=createTimeSpan(0,0,10,0) {
    // Expensive rendering
    var products = queryExecute("SELECT * FROM products")
    for ( var p in products ) {
        include "views/product-card.bxm"
    }
}

// With a custom cache key
bx:cache timespan=createTimeSpan(0,0,5,0) id="dashboard_#session.userId#" {
    renderDashboard()
}

// Named cache region for output
bx:cache timespan=createTimeSpan(0,1,0,0) cacheName="pages" {
    renderHomePage()
}
```

## Redis Caching (bx-redis module)

```json
// boxlang.json — configure Redis cache
{
    "modules": {
        "bx-redis": {
            "enabled": true,
            "settings": {
                "host": "${REDIS_HOST:localhost}",
                "port": "${REDIS_PORT:6379}",
                "password": "${REDIS_PASSWORD:}",
                "database": 0
            }
        }
    },
    "caches": {
        "distributed": {
            "provider": "redis",
            "properties": {
                "defaultTimeout": 300,
                "keyPrefix": "myapp:"
            }
        }
    }
}
```

```boxlang
// Use Redis cache transparently via the same API
cachePut( "session_#token#", sessionData, createTimeSpan(0,2,0,0), "", "distributed" )
var sessionData = cacheGet( "session_#token#", false, "distributed" )
```

## Distributed Locking

Prevent cache stampede with distributed locks:

```boxlang
// Only one process rebuilds the cache at a time
bx:lock name="rebuild_product_cache" type="exclusive" timeout=30 {
    // Double-check after acquiring the lock
    var cached = cacheGet( "all_products" )
    if ( isNull( cached ) ) {
        var products = queryExecute( "SELECT * FROM products" )
        cachePut( "all_products", products, createTimeSpan(0,0,30,0) )
    }
}
```

## Couchbase (bx-couchbase module)

```json
// boxlang.json
{
    "modules": {
        "bx-couchbase": {
            "enabled": true,
            "settings": {
                "servers": ["couchbase://localhost"],
                "bucket": "myapp",
                "username": "${COUCH_USER}",
                "password": "${COUCH_PASS}"
            }
        }
    },
    "caches": {
        "docStore": {
            "provider": "couchbase",
            "properties": {
                "defaultTimeout": 3600
            }
        }
    }
}
```

## Eviction Policies

| Policy | Description |
|--------|-------------|
| `LRU` | Least Recently Used (default) |
| `LFU` | Least Frequently Used |
| `FIFO` | First-In, First-Out |
| `Random` | Random eviction |

## Cache Warming on Application Start

```boxlang
// Application.bx
boolean function onApplicationStart() {
    // Pre-load frequently accessed data
    var config = configService.loadAll()
    cachePut( "app_config", config, createTimeSpan(1,0,0,0) )

    var categories = queryExecute( "SELECT * FROM categories" )
    cachePut( "categories", categories, createTimeSpan(0,4,0,0) )

    return true
}
```

## Cache Invalidation Patterns

```boxlang
// Tag-based invalidation (group related keys)
function invalidateUserCache( required numeric userId ) {
    cacheRemove( "user_#userId#" )
    cacheRemove( "user_profile_#userId#" )
    cacheRemove( "user_permissions_#userId#" )
    cacheRemove( "user_orders_#userId#" )
}

// Versioned cache keys (avoids stampedes)
function getCacheKey( required string base ) {
    var version = cacheGet( "v:#base#" ) ?: 1
    return "#base#:v#version#"
}

function invalidateVersion( required string base ) {
    var current = cacheGet( "v:#base#" ) ?: 1
    cachePut( "v:#base#", current + 1, createTimeSpan(1,0,0,0) )
}
```

## `cacheGetOrSet` — Atomic Get-or-Load

`cacheGetOrSet()` atomically returns a cached value or executes a loader closure
and stores the result if no entry exists:

```boxlang
// Signature: cacheGetOrSet( key, valueFunction, timeout, cacheName )
var settings = cacheGetOrSet(
    "appSettings",
    () => settingService.loadAll(),
    60  // minutes
)

// With named cache region
var product = cacheGetOrSet(
    "product_#id#",
    () => queryExecute( "SELECT * FROM products WHERE id = :id", { id: id }, { returntype: "struct" } ),
    createTimeSpan( 0, 1, 0, 0 ),
    "products"
)
```

## Query Caching via `cachedWithin`

Cache query results directly in `queryExecute()` options:

```boxlang
// Cache for 1 hour — result is reused on subsequent calls
var users = queryExecute(
    "SELECT * FROM users WHERE isActive = 1",
    {},
    { cachedWithin: createTimeSpan( 0, 1, 0, 0 ) }
)

// Cache with a named key (use cacheRemove() to invalidate)
var products = queryExecute(
    "SELECT * FROM products ORDER BY name",
    {},
    {
        cacheName    : "productList",
        cachedWithin : createTimeSpan( 0, 0, 30, 0 )
    }
)

// Invalidate manually when data changes
function updateProduct( required numeric id, required struct data ) {
    queryExecute( "UPDATE products SET name = :name WHERE id = :id",
                  { name: data.name, id: id } )
    cacheRemove( "productList" )
}
```

## Function-Level Memoization

Use the `cachedWithin` function attribute to cache a function's return value
based on its arguments:

```boxlang
// Method result cached per unique argument set for 1 hour
function fibonacci( required numeric n ) cachedWithin=createTimeSpan( 0, 1, 0, 0 ) {
    if ( n <= 1 ) return n
    return fibonacci( n - 1 ) + fibonacci( n - 2 )
}

// Manual in-memory memoization (no TTL)
class ExpensiveService {
    variables.memo = {}

    function compute( required numeric id ) {
        if ( memo.keyExists( id ) ) return memo[ id ]
        var result = runExpensiveCalculation( id )
        memo[ id ] = result
        return result
    }
}
```

## References

- [Caching Framework](https://boxlang.ortusbooks.com/boxlang-framework/caching)
- [Cache BIFs](https://boxlang.ortusbooks.com/boxlang-language/reference/built-in-functions/cache)
- [bx-redis Module](https://boxlang.ortusbooks.com/boxlang-framework/boxlang-plus/modules/bx-redis)
- [bx-couchbase Module](https://boxlang.ortusbooks.com/boxlang-framework/boxlang-plus/modules/bx-couchbase)