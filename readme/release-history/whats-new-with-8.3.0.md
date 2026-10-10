---
description: Upcoming release
---

# What's New With 8.3.0

ColdBox 8.3.0 is a feature release focused on testing and route composition: **real-browser testing** of ColdBox applications on BoxLang, named route middleware with `Router.registerMiddleware()`, route metadata shared by a whole `group()`, and integration tests that now run route middleware exactly like a real request. It also hardens WireBox scope wiring under concurrency and fixes a set of engine-specific and scheduler bugs.

## Major Highlights

### 🌐 Browser Testing With @browser (BoxLang)

{% hint style="warning" %}
🚀 **BoxLang Exclusive**: browser testing requires **BoxLang**, the **bx-playwright** module and a TestBox release with annotation-driven browser support (**TestBox 7.2.0+**, [TestBox#222](https://github.com/Ortus-Solutions/TestBox/pull/222)). On older TestBox releases the annotations do nothing and `browse()` is not defined.
{% endhint %}

There is no separate browser test class. Annotate any `coldbox.system.testing.BaseTestCase` spec with `@browser`, `@browserProfile` or `@baseURL` and TestBox attaches its browser support: `browse()`, `this.playwright()`, `browserAvailable()` and the retrying browser matchers. The spec still loads your application like any integration test, so it knows your routes, while it drives a real browser against your **running** application. `BaseTestCase` adds the named route helpers `routeURL()`, `visitRoute()` and `assertRouteIs()`, module routes included, and logged-in tests use bx-playwright saved sessions.

```javascript
@appMapping( "/root" )
@browser
@baseURL( "http://127.0.0.1:8080" )
class extends="coldbox.system.testing.BaseTestCase" {

	function run() {
		describe( "Users", () => {
			it( "shows a user", () => {
				browse( ( page ) => {
					visitRoute( page, "users.show", { id : 5 } )
					assertRouteIs( page, "users.show", { id : 5 } )
					expect( page ).toSee( "User 5" )
				} )
			} )
		} )
	}

}
```

See the [Browser Testing](../../the-basics/testing-quick-start/browser-testing/README.md) guide, including [Named Routes](../../the-basics/testing-quick-start/browser-testing/named-routes.md) and [Authentication](../../the-basics/testing-quick-start/browser-testing/authentication.md).

### 🏷️ Named Route Middleware: `registerMiddleware()`

`Router.registerMiddleware()` registers a closure, lambda, object instance or WireBox ID under a name, so routes and groups reference it by that name instead of repeating the target. A registered name works everywhere a `middlewareGroup()` name does: `.middleware()`, a group's `middleware` option and `.withoutMiddleware()`. Pass a struct of `name : target` pairs to register several at once.

```javascript
function configure(){
    registerMiddleware( "onlyJson", ( event, rc, prc ) => {
        if ( !event.isAjax() ) {
            event.renderData( type = "json", data = { "error" : "JSON only" }, statusCode = 406 ).noExecution();
        }
    } );

    group( { pattern : "/api", middleware : [ "onlyJson" ] }, () => {
        route( "/users" ).toHandler( "users" );
        route( "/health" ).withoutMiddleware( "onlyJson" ).toHandler( "health" );
    } );
}
```

Registering a name that already exists, including a `middlewareGroup()` name, throws `Router.DuplicateMiddleware` unless you pass `force = true`. Like `middlewareGroup()`, register names before the routes that reference them. This release also fixes passing a component instance directly to `.middleware()` on engines where `isStruct()` is true for components. See [Middleware Groups & Exclusions](../../the-basics/routing/routing-dsl/middleware-groups.md#named-middleware).

### 🧩 Group Route Metadata: `meta`

`group()` now accepts a `meta` struct that every route inside the group inherits. Nested groups merge outer-first and a route's own `.meta()` values win on conflict, so one group can declare the metadata your middleware or security rules consume.

```javascript
group( { pattern : "/admin", meta : { permissions : "ADMIN" } }, () => {
    route( "/users" ).to( "admin.users" );                                      // { permissions : "ADMIN" }
    route( "/reports" ).meta( { permissions : "REPORTS" } ).to( "admin.reports" ); // route wins
} );

// In a handler or middleware
var perms = event.getCurrentRouteMeta().permissions;
```

See [Routing Groups](../../the-basics/routing/routing-dsl/routing-groups.md#sharing-route-metadata).

### 🧪 Integration Tests Run Route Middleware

`BaseTestCase.execute()`, and the `get()`, `post()` and other HTTP helpers built on it, now run route-scoped middleware registered with `.middleware()`, in the same order as a real request: after the global `preProcess` announcement and before the global `postProcess` announcement. A route protected by middleware is now protected in your integration tests too.

```javascript
it( "blocks anonymous users from the admin", () => {
    var event = get( "/admin" );
    expect( event.getRenderData().statusCode ).toBe( 403 );
} );
```

{% hint style="warning" %}
If you have integration tests that hit routes with middleware attached, those tests now see the middleware. A test that expected to reach the handler of a protected route without satisfying its middleware will now be blocked, exactly like a real request.
{% endhint %}

See [The execute() Method](../../the-basics/testing-quick-start/integration-testing/the-execute-method.md#route-middleware).

### 🔒 Thread-Safe WireBox Scope Wiring

Singleton, engine (application, session, server) and CacheBox scopes store an object before wiring it so circular dependencies resolve. Other threads could read that object before its dependencies were injected and get a half-wired instance. A single wiring lock per injector now makes other threads wait until wiring completes, while the wiring thread still resolves circular dependencies. Circular partners also stay marked as wiring until the outermost build completes, across all scopes.

### ⏰ Scheduler: `onOneServer()` Calendar Tasks No Longer Drift

Calendar tasks (`everyDayAt()`, `everyHourAt()`, `everyWeekOn()`, `everyMonthOn()`, `everyYearOn()` and the business-day helpers) combined with `onOneServer()` drifted to the scheduler's restart time after a restart. They now keep their configured wall-clock time. Plain `every( n, unit )` tasks still sync with the cluster lock.

### ⚠️ Compatibility: `CFScopes` Renamed to `EngineScopes`

The WireBox scope class that stores objects in the application, session and server scopes was renamed from `coldbox.system.ioc.scopes.CFScopes` to `coldbox.system.ioc.scopes.EngineScopes`. The scope **names** you use in `scope="session"`, `scope="application"`, `scope="server"` or `.into( this.SCOPES.SESSION )` are unchanged, so most applications are not affected.

{% hint style="danger" %}
There is no alias for the old class: `CFScopes.cfc` was removed. If you extend, instantiate or reference `coldbox.system.ioc.scopes.CFScopes` directly, for example in a custom scope, change it to `coldbox.system.ioc.scopes.EngineScopes`.
{% endhint %}

## Release Notes

{% tabs %}
{% tab title="ColdBox" %}
### Added

[COLDBOX-1457](https://ortussolutions.atlassian.net/browse/COLDBOX-1457) Browser testing for ColdBox apps: `@browser`, `@browserProfile` and `@baseURL` on `BaseTestCase` and the named route helpers `routeURL()`, `visitRoute()` and `assertRouteIs()` (BoxLang, TestBox 7.2.0+, bx-playwright) ([#708](https://github.com/ColdBox/coldbox-platform/pull/708))

[COLDBOX-1459](https://ortussolutions.atlassian.net/browse/COLDBOX-1459) `group()` accepts a `meta` struct inherited by every route inside it; nested groups merge outer-first and a route's own `meta()` wins ([#714](https://github.com/ColdBox/coldbox-platform/pull/714))

[COLDBOX-1460](https://ortussolutions.atlassian.net/browse/COLDBOX-1460) `Router.registerMiddleware()` registers a closure, lambda, object instance or WireBox ID as named route middleware, singly or as a struct; duplicates throw `Router.DuplicateMiddleware` unless `force = true` ([#719](https://github.com/ColdBox/coldbox-platform/pull/719))

### Fixed

[COLDBOX-1459](https://ortussolutions.atlassian.net/browse/COLDBOX-1459) `BaseTestCase.execute()` and the HTTP helpers built on it did not run route-scoped middleware ([#714](https://github.com/ColdBox/coldbox-platform/pull/714))

[COLDBOX-1460](https://ortussolutions.atlassian.net/browse/COLDBOX-1460) Passing a component instance as route middleware failed on engines where `isStruct()` is true for components ([#719](https://github.com/ColdBox/coldbox-platform/pull/719))

[COLDBOX-1456](https://ortussolutions.atlassian.net/browse/COLDBOX-1456) `Bootstrap.onSessionStart()` ran the session start handler on a controller that was still loading during a reinit; it now skips the event until the controller is initiated ([#716](https://github.com/ColdBox/coldbox-platform/pull/716))

[COLDBOX-1454](https://ortussolutions.atlassian.net/browse/COLDBOX-1454) Interceptor buffer pool race between a request and its async `announce()` thread ("can not pop Element from array, array is empty") ([#705](https://github.com/ColdBox/coldbox-platform/pull/705))

[COLDBOX-1453](https://ortussolutions.atlassian.net/browse/COLDBOX-1453) `RestHandler.onEntityNotFoundException()` threw `MissingArgumentException` on Adobe ColdFusion 2023 ([#704](https://github.com/ColdBox/coldbox-platform/pull/704))

[COLDBOX-1458](https://ortussolutions.atlassian.net/browse/COLDBOX-1458) `onOneServer()` calendar tasks (`everyDayAt()`, `everyMonthOn()`, etc.) drifted to the scheduler's restart time after a restart ([#713](https://github.com/ColdBox/coldbox-platform/pull/713))

`event.route( "name@module" )` built module route links without a slash between the module entry point and the route pattern ([#708](https://github.com/ColdBox/coldbox-platform/pull/708))

Adobe ColdFusion: a request context decorator copied the `this` reference of the original context, so its inherited methods ran against the original context and missed the decorator's own state and mocks ([#708](https://github.com/ColdBox/coldbox-platform/pull/708))
{% endtab %}

{% tab title="WireBox" %}
### Changed

The `CFScopes` scope class was renamed to `EngineScopes` (`coldbox.system.ioc.scopes.EngineScopes`). Scope names are unchanged; there is no alias for the old class path ([#715](https://github.com/ColdBox/coldbox-platform/pull/715))

### Fixed

[COLDBOX-1455](https://ortussolutions.atlassian.net/browse/COLDBOX-1455) Singleton, engine (application, session, server) and CacheBox scopes handed objects to other threads before their dependencies were wired; a single wiring lock now makes other threads wait ([#715](https://github.com/ColdBox/coldbox-platform/pull/715))

A circular partner was released to other threads before the outer object finished wiring; keys now stay marked until the outermost build completes, tracked on the injector across all scopes ([#718](https://github.com/ColdBox/coldbox-platform/pull/718))
{% endtab %}
{% endtabs %}
