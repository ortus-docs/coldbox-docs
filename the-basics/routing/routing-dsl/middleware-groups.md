---
description: >-
  Register reusable, named middleware bundles and opt individual routes out
  of middleware they'd otherwise inherit.
---

# Middleware Groups & Exclusions

Once you're attaching the same [middleware](middleware.md) list to more than one route or group, naming it once beats repeating it everywhere.

## Registering a Named Group

`middlewareGroup( name, [ ...targets ] )` registers a reusable bundle. Reference it by name from `.middleware()` or a group's `middleware` option, exactly like any other target:

```javascript
middlewareGroup( "api", [ "RequireApiKey", "RateLimiter" ] );

route( "/orders" ).middleware( "api" ).toHandler( "orders" );

group( { pattern : "/api", middleware : [ "api" ] }, () => {
    route( "/users" ).toHandler( "users" );
} );
```

{% hint style="danger" %}
**Register the group before referencing it.** Expansion happens immediately, at registration time - a name referenced before its `middlewareGroup()` call is silently treated as a literal target (e.g. a WireBox ID) instead of being expanded. Declare your groups at the top of `configure()`.
{% endhint %}

Groups are flat - a member can't itself be the name of another group. Each entry is a concrete closure, WireBox ID, or object.

## Named Middleware

`registerMiddleware( name, target, [point], [force] )` registers a **single** target under a name. The target can be a closure, a lambda, an object instance or a WireBox ID. A registered name works everywhere a `middlewareGroup()` name does: `.middleware()`, a group's `middleware` option and `withoutMiddleware()`. It is the way to give a closure or an instance a name, so it can be reused and excluded.

```javascript
registerMiddleware( "onlyJson", ( event, rc, prc ) => {
    if ( !event.isAjax() ) {
        event.renderData( type = "json", data = { "error" : "JSON only" }, statusCode = 406 ).noExecution();
    }
} );

// A WireBox ID, or an object with a preProcess()/postProcess() method
registerMiddleware( "auth", "Authenticated@cbsecurity" );

// Run after the handler instead of before it
registerMiddleware( "auditLog", "AuditLog", "postProcess" );

route( "/admin" ).middleware( "auth" ).toHandler( "admin" );

group( { pattern : "/api", middleware : [ "onlyJson" ] }, () => {
    route( "/health" ).withoutMiddleware( "onlyJson" ).toHandler( "health" );
} );
```

Pass a struct of `name : target` pairs to register several at once. `point` and `force` then apply to every entry, and the call is all or nothing: if one name is a duplicate, nothing is registered. CFML cannot mix positional and named arguments, so name every argument when you pass `force`:

```javascript
registerMiddleware( name = { auth : "Authenticated@cbsecurity", onlyJson : jsonClosure }, force = true );
```

* Registered names and `middlewareGroup()` names share one namespace. Registering a name that already exists throws a `Router.DuplicateMiddleware` exception unless `force = true`, which replaces it. Routes declared earlier keep the old definition.
* An empty name or a missing target throws `Router.InvalidMiddleware`.
* As with groups, register the name **before** the routes that reference it.
* The registry belongs to the router instance it was called on. In modules, map your middleware in WireBox and reference it by WireBox ID instead.

`registerMiddleware()` is available since ColdBox 8.3.0.

## Excluding Inherited Middleware

`withoutMiddleware()` opts a single route out of middleware it would otherwise inherit - from an enclosing group, or from its own earlier `.middleware()` calls.

```javascript
group( { pattern : "/api", middleware : [ "api" ] }, () => {
    route( "/users" ).toHandler( "users" );                              // runs "api"
    route( "/health" ).withoutMiddleware( "api" ).toHandler( "health" ); // opts out
} );
```

Match by the same name used to attach the middleware:

* **A WireBox ID** - excludes that one target
* **A `middlewareGroup()` name** - excludes every member that group expanded to, not just a same-named single target
* **`"*"`** - strips everything for that route, inherited or its own

```javascript
route( "/public" )
    .middleware( "RateLimiter" )
    .withoutMiddleware( "*" )
    .toHandler( "public" );
```

{% hint style="info" %}
Closures and object instances attached directly have no name to match, so they can only be kept off a route by not attaching them in the first place. Register them with [`registerMiddleware()`](#named-middleware) and attach them by name to make them excludable.
{% endhint %}

Call order doesn't matter - `withoutMiddleware()` and `.middleware()` can appear anywhere in the fluent chain and the exclusion is still applied once the route finishes registering.
