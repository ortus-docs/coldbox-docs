---
description: >-
  Attach middleware to a route: closures, WireBox IDs, or plain objects that
  run before or after the matched handler.
---

# Route Middleware

`.middleware()` attaches a target to a route that runs at request time, scoped to just that route. It's not a new subsystem - it reuses the same interception dispatch ColdBox interceptors already use, just scoped to one route instead of the whole app.

```javascript
route( "/admin/:action" )
    .middleware( function( event, rc, prc ){
        if ( !auth.isLoggedIn() ) {
            event.relocate( "login" );
            return true; // stop the remaining middleware for this route
        }
    } )
    .toHandler( "admin" );
```

## A Target Can Be

* **A closure/lambda** - `function( event, rc, prc ){ ... }`, as above
* **A WireBox ID** - resolved via `getInstance()` on every request, so it respects whatever scope (singleton, prototype, etc) the mapping was registered with
* **Any object** - WireBox-managed or not, as long as it has a method named after the point it runs at (`preProcess()` by default). No base class or interface required

```javascript
// A concrete class, resolved by WireBox ID
component singleton {
    property name="auth" inject="AuthService";

    function preProcess( event, rc, prc ){
        if ( !auth.isLoggedIn() ) {
            event.relocate( "login" );
            return true;
        }
    }
}
// registered in WireBox as "RequireLogin"
route( "/admin/:action" ).middleware( "RequireLogin" ).toHandler( "admin" );
```

## Where It Runs

A target attaches to a **point** - `preProcess` (default) or `postProcess` - the second argument to `.middleware()`:

```javascript
// Runs before the handler - the default
route( "/api/orders" ).middleware( "RateLimiter" ).toHandler( "orders" );

// Runs after the handler, to shape the response
route( "/api/reports" ).middleware( "AuditLog", "postProcess" ).to( "reports.index" );
```

Route middleware runs **after** the global `preProcess` announce and **before** the global `postProcess` announce - global interceptors stay the outermost layer, route-specific work happens closest to the handler.

## Multiple Targets

Pass an array to attach several targets to the same point in one call. They run in order:

```javascript
route( "/api/orders" )
    .middleware( [ "RateLimiter", "RequireApiKey" ] )
    .toHandler( "orders" );
```

## Short-Circuiting

Returning `true` from a target stops the **remaining middleware for that route at that point**. It does **not**, by itself, skip the handler or the render - call `event.relocate()`, `event.renderData().noExecution()`, or similar, exactly as you would from any other `preProcess`/`postProcess` interceptor:

```javascript
route( "/admin/:action" ).middleware( function( event, rc, prc ){
    if ( !auth.isLoggedIn() ) {
        event.relocate( "login" );
        return true; // no further middleware runs for this route
    }
} ).toHandler( "admin" );
```

{% hint style="info" %}
Middleware is a flat before/after dispatch, not a wrapping pipeline - a single target can't run code both before **and** after the handler in one call. If you need genuine wrapping (timing a whole request, catching everything downstream), reach for `aroundHandler` on the handler itself instead.
{% endhint %}

## Sharing Middleware Across Routes

Attaching `middleware` to a [group](routing-groups.md) applies it to every route declared inside, ahead of each route's own middleware:

```javascript
group( { pattern : "/api", middleware : [ "RequireApiKey" ] }, function(){
    route( "/users" ).middleware( "RateLimiter" ).toHandler( "users" ); // RequireApiKey, then RateLimiter
    route( "/products" ).toHandler( "products" );                      // RequireApiKey only
} );
```

Nested groups compose outer-first:

```javascript
group( { pattern : "/api", middleware : [ "RequireApiKey" ] }, function(){
    group( { pattern : "/admin", middleware : [ "RequireAdmin" ] }, function(){
        route( "/users" ).toHandler( "users" ); // RequireApiKey, then RequireAdmin
    } );
} );
```

{% hint style="success" %}
**Next:** reusing the same middleware list by name across unrelated routes, and opting a route out of what it would otherwise inherit - see [Middleware Groups & Exclusions](middleware-groups.md).
{% endhint %}
