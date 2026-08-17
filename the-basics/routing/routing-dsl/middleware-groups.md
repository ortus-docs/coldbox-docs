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

group( { pattern : "/api", middleware : [ "api" ] }, function(){
    route( "/users" ).toHandler( "users" );
} );
```

{% hint style="danger" %}
**Register the group before referencing it.** Expansion happens immediately, at registration time - a name referenced before its `middlewareGroup()` call is silently treated as a literal target (e.g. a WireBox ID) instead of being expanded. Declare your groups at the top of `configure()`.
{% endhint %}

Groups are flat - a member can't itself be the name of another group. Each entry is a concrete closure, WireBox ID, or object.

## Excluding Inherited Middleware

`withoutMiddleware()` opts a single route out of middleware it would otherwise inherit - from an enclosing group, or from its own earlier `.middleware()` calls.

```javascript
group( { pattern : "/api", middleware : [ "api" ] }, function(){
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
Closures and object instances have no name to match, so they can only be kept off a route by not attaching them in the first place.
{% endhint %}

Call order doesn't matter - `withoutMiddleware()` and `.middleware()` can appear anywhere in the fluent chain and the exclusion is still applied once the route finishes registering.
