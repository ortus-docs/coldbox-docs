---
description: >-
  Attach middleware to a route: closures, WireBox IDs, or plain objects that
  run before or after the matched handler.
---

# Route Middleware

`.middleware()` attaches a target to a route that runs at request time, scoped to just that route. It's not a new subsystem - it reuses the same interception dispatch ColdBox interceptors already use, just scoped to one route instead of the whole app.

```javascript
route( "/admin/:action" )
    .middleware( ( event, rc, prc ) => {
        if ( !auth.isLoggedIn() ) {
            event.relocate( "login" );
            return true; // stop the remaining middleware for this route
        }
    } )
    .toHandler( "admin" );
```

## A Target Can Be

* **A closure/lambda** - `( event, rc, prc ) => { ... }`, as above
* **A WireBox ID** - resolved via `getInstance()` on every request, so it respects whatever scope (singleton, prototype, etc) the mapping was registered with
* **Any object** - WireBox-managed or not, as long as it has a method named after the point it runs at (`preProcess()` by default). No base class or interface required

{% tabs %}
{% tab title="BoxLang" %}
```js
// A concrete class, resolved by WireBox ID
class singleton {
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
{% endtab %}
{% tab title="CFML" %}
```cfscript
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
{% endtab %}
{% endtabs %}

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
route( "/admin/:action" ).middleware( ( event, rc, prc ) => {
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
group( { pattern : "/api", middleware : [ "RequireApiKey" ] }, () => {
    route( "/users" ).middleware( "RateLimiter" ).toHandler( "users" ); // RequireApiKey, then RateLimiter
    route( "/products" ).toHandler( "products" );                      // RequireApiKey only
} );
```

Nested groups compose outer-first:

```javascript
group( { pattern : "/api", middleware : [ "RequireApiKey" ] }, () => {
    group( { pattern : "/admin", middleware : [ "RequireAdmin" ] }, () => {
        route( "/users" ).toHandler( "users" ); // RequireApiKey, then RequireAdmin
    } );
} );
```

## Registering Named Middleware

`registerMiddleware( name, target )` gives a single closure, lambda, object, or WireBox ID a name, so you can reference it from many routes without repeating it. The target is any of the [targets above](#a-target-can-be), and the optional third argument is the point (`preProcess` by default, or `postProcess`).

{% tabs %}
{% tab title="BoxLang" %}
```javascript
registerMiddleware( "OnlyJson", ( event, rc, prc ) => {
    if ( !event.isAjax() ) {
        event.renderData( type = "json", data = { "error" : "JSON only" }, statusCode = 406 ).noExecution()
        return true
    }
} )

route( "/api/orders" ).middleware( "OnlyJson" ).toHandler( "orders" )

group( { pattern : "/api", middleware : [ "OnlyJson" ] }, () => {
    route( "/users" ).toHandler( "users" )
    route( "/feed.xml" ).withoutMiddleware( "OnlyJson" ).toHandler( "feed" ) // opts out
} )
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
registerMiddleware( "OnlyJson", function( event, rc, prc ){
    if ( !event.isAjax() ) {
        event.renderData( type = "json", data = { "error" : "JSON only" }, statusCode = 406 ).noExecution();
        return true;
    }
} );

route( "/api/orders" ).middleware( "OnlyJson" ).toHandler( "orders" );

group( { pattern : "/api", middleware : [ "OnlyJson" ] }, function(){
    route( "/users" ).toHandler( "users" );
    route( "/feed.xml" ).withoutMiddleware( "OnlyJson" ).toHandler( "feed" ); // opts out
} );
```
{% endtab %}
{% endtabs %}

A registered name works anywhere a [middleware group](middleware-groups.md) name does: in `.middleware()`, in a group's `middleware` option, and in `withoutMiddleware( name )`. Under the hood it is stored as a one-member group in the same namespace, and `registerMiddleware()` returns the router so calls can be chained.

### Registering Several at Once

Pass a struct of `name : target` pairs. Every entry uses the same point (`preProcess` unless you pass one):

```javascript
registerMiddleware( {
    auth     : "Authenticated@cbsecurity",
    onlyJson : ( event, rc, prc ) => { /* ... */ }
} )
```

{% hint style="info" %}
CFML does not allow mixing positional and named arguments. To pass `force` with the struct form, name every argument: `registerMiddleware( name = { auth : "Authenticated@cbsecurity" }, force = true )`.
{% endhint %}

### Duplicate Names

Registering a name that is already taken, either by another `registerMiddleware()` call or by a `middlewareGroup()`, throws a `Router.DuplicateMiddleware` exception. Pass `force = true` to replace the existing definition. A bulk call is all or nothing: if any name is a duplicate, nothing from that call is registered. A missing or empty name or target throws `Router.InvalidMiddleware`.

```javascript
registerMiddleware( "auth", "Authenticated@cbsecurity" )
registerMiddleware( "auth", "Other" )                        // throws Router.DuplicateMiddleware
registerMiddleware( "auth", "Other", "preProcess", true )    // overwrites
```

{% hint style="danger" %}
**Register the name before referencing it.** Like `middlewareGroup()`, the name is resolved immediately when `.middleware()` or `group()` runs, not at request time. A name that is not registered yet is silently treated as a literal WireBox ID. Routes declared before a `force` overwrite keep the old definition.
{% endhint %}

### Middleware From Modules

The registry belongs to the router that registered it, and modules do not share one. A module that ships middleware should register it as a WireBox mapping and let apps reference the WireBox ID, as `Authenticated@cbsecurity` does. An application can then give that ID a short local name:

```javascript
registerMiddleware( "auth", "Authenticated@cbsecurity" )
route( "/account" ).middleware( "auth" ).to( "account.index" )
```

{% hint style="info" %}
`registerMiddleware()` requires ColdBox 8.3+.
{% endhint %}

## Securing Routes With cbsecurity

The [cbsecurity](../../../digging-deeper/security/README.md) module ships ready-made middleware, so you do not write login and permission checks yourself. Permissions and roles are declared in the route's `meta()`:

```javascript
route( "/account" ).middleware( "Authenticated@cbsecurity" ).to( "account.index" )

route( "/admin" )
    .middleware( "Authorized@cbsecurity" )
    .meta( { permissions : "ADMIN" } )
    .to( "admin.index" )
```

### cbsecurity Middleware Reference

Every cbsecurity middleware is a WireBox ID you pass to `middleware()`. Parameters go in the route's `meta()`, because modules load after the router. Each name links to its cbsecurity guide.

| WireBox ID | What it does | Route `meta()` keys |
| --- | --- | --- |
| [`Authenticated@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware) | The user must be logged in. | none |
| [`Authorized@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware) | Logged in and has the permissions or roles. | `permissions`, `roles`, `mode` |
| [`JwtAuth@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware) | Like `Authorized`, authenticating with a JWT. | `permissions`, `mode` |
| [`BasicAuth@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware) | Like `Authorized`, authenticating with HTTP Basic. | `permissions`, `roles`, `mode` |
| [`Throttle@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/throttle) | Rate limits requests, answering `429` over the limit. | `throttle` |
| [`ApiKey@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/api-key) | Requires an API key from the `x-api-key` header or `apiKey` request key. | `apiKeys`, `apiKeyHeader`, `apiKeyParam` |
| [`AllowedIPs@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/ip-filtering) | Only listed IPs and CIDR ranges get in. | `allowedIps` |
| [`DenyIPs@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/ip-filtering) | Blocks listed IPs and CIDR ranges. | `denyIps` |
| [`EnsureHttps@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/ensure-https) | Redirects to HTTPS, or denies non `GET` requests. | `redirectToHttps` |
| [`VerifyCsrf@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/verify-csrf) | Verifies a CSRF token on unsafe requests. | `csrfKey` |
| [`Honeypot@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/honeypot) | Catches spam bots with a hidden form field. | `honeypotField`, `honeypotSilent` |
| [`Signed@cbsecurity`](https://coldbox-security.ortusbooks.com/usage/route-middleware/signed-urls) | Only lets valid, unexpired signed URLs through. | none |

Stack them. They run in the order you list them, so put cheap checks first:

```javascript
route( "/api/orders" )
    .middleware( [ "DenyIPs@cbsecurity", "Throttle@cbsecurity", "JwtAuth@cbsecurity" ] )
    .meta( {
        denyIps     : "198.51.100.0/24",
        throttle    : { maxAttempts : 120, decaySeconds : 60 },
        permissions : "ORDERS_READ"
    } )
    .to( "orders.index" )
```

The authentication middleware send denied requests through the cbsecurity firewall's invalid action (redirect, override or block). The others answer directly with a JSON error and the right status code.

{% hint style="info" %}
`Throttle@cbsecurity` and `Signed@cbsecurity` also come with models and helpers you can use outside routes: `RateLimiter@cbsecurity`, `UrlSigner@cbsecurity` and the `signedRoute()`, `signedUrl()` and `hasValidSignature()` helpers.
{% endhint %}

Defaults for these middleware (trusted proxies, API key header, throttle limiters, the signing secret) live in the cbsecurity [route middleware settings](https://coldbox-security.ortusbooks.com/getting-started/configuration/middleware). The full guide is the cbsecurity [Route Middleware](https://coldbox-security.ortusbooks.com/usage/route-middleware) page.

## Testing Routes With Middleware

The integration test helpers (`execute()`, `get()`, `post()` and friends) run route middleware in the same order as a real request, so you can assert on a blocked or redirected route.

{% hint style="info" %}
Running route middleware inside `execute()` requires ColdBox 8.3+. On earlier versions the middleware is skipped in integration tests.
{% endhint %}

{% hint style="success" %}
**Next:** reusing the same middleware list by name across unrelated routes (bundles registered with `middlewareGroup()`), and opting a route out of what it would otherwise inherit - see [Middleware Groups & Exclusions](middleware-groups.md).
{% endhint %}
