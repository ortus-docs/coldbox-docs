---
description: The official ColdBox 8 upgrade guide
---

# Upgrading to ColdBox 8

The major compatibility issues will be covered, as well as how to upgrade to this release from previous ColdBox versions smoothly. You can also check out the [What's New](https://github.com/ortus-docs/coldbox-docs/blob/v8.x/readme/release-history/whats-new-with-8.0.0) guide to give you a full overview of the changes.

An upgrade from ColdBox 7 should not incur any breaking changes, but you should still read through the guide to ensure you are not using any deprecated or removed features.

## ColdFusion 2018 Support Dropped

ColdFusion 2018 support has been dropped. Adobe doesn't support them anymore, so neither do we.

## Removals

### CacheBox Tag Interfaces: ICacheProvider, IStats

The old interfaces that had been marked for deprecation in 6 are now removed. If you have custom cache providers or stats providers, you will need to update them to extend from the base classes:

* `coldbox.system.cache.ICacheProvider` -> `coldbox.system.cache.providers.ICacheProvider`
* `coldbox.system.cache.IStats` -> `coldbox.system.cache.util.IStats`

### BeanPopulator

The `BeanPopulator` class has been removed.  This class was deprecated in ColdBox 6 and was replaced by the `ObjectPopulator` class.  Please use `coldbox.system.core.dynamic.ObjectPopulator` instead.

### Client Flash

The Client Flash has been removed as it was deprecated in v6.  The `client` scope is a very old, unperformant and insecure way of storing data.  We recommend using `CacheBox` or `Session` scope instead.

### ColdBox Util Env/System Methods

The following methods were removed in preference to the Environment Delegate class: `coldbox.system.core.delegates.Env`.

```js
/**
* @deprecated Refactor to use the Env Delegate: coldbox.system.core.delegates.Env
*/
function getSystemSetting( required key, defaultValue ){
    return new coldbox.system.core.delegates.Env().getSystemSetting( argumentCollection = arguments );
}

/**
* @deprecated Refactor to use the Env Delegate: coldbox.system.core.delegates.Env
*/
function getSystemProperty( required key, defaultValue ){
    return new coldbox.system.core.delegates.Env().getSystemProperty( argumentCollection = arguments );
}

/**
* @deprecated Refactor to use the Env Delegate: coldbox.system.core.delegates.Env
*/
function getEnv( required key, defaultValue ){
    return new coldbox.system.core.delegates.Env().getEnv( argumentCollection = arguments );
}
```

### Binder.getProperty() `default` Argument Removed

The `default` argument was deprecated in ColdBox 6 and has now been removed.  Please use `defaultValue` instead.

### Binder.getCacheBoxConfig() Removed

The `getCacheBoxConfig()` method was deprecated in ColdBox 6 and has now been removed.  Please use `getCacheBox()` instead.

### RequestContext SES Methods Removed

The following methods were removed from the `RequestContext` class.  These methods were deprecated in ColdBox 7.

* `isSES()`
* `setSESEnabled()`

### Router.getModulesRoutingTable() Removed

The `getModulesRoutingTable()` method was deprecated in ColdBox 7 and has now been removed.  Please use `getModuleRoutingTable()` instead.

### Router.includeRoutes() removed

The `includeRoutes()` method was deprecated in ColdBox 6 and has now been removed.  This was for `cfm` routers which are no longer in use.

### Router.with() and endWith() removed

The `with()` and `endWith()` methods were deprecated in ColdBox 7 and have now been removed.  Please use the `group()` method with closures instead.

```js
// Old way - removed
with( namespace = "luis" )
    .addRoute(
        pattern = "contactus",
        view    = "simpleview"
    )
    .addRoute(
        pattern      = "contactus2",
        view         = "simpleview",
        viewnoLayout = true
    )
    .endWith();

// New way - use group() with closure
group( { namespace : "luis" }, ( options ) => {
    route( pattern: "contactus" ).toView( view: "simpleview" );
    route( pattern: "contactus2" ).toView( view: "simpleview", noLayout: true );
} );

```

### Router.addRoute() `matchVariables` Argument Removed

The `matchVariables` string argument that mimicked a query string was deprecated in ColdBox 6 and has now been removed.  Please use the `rc` or `prc` struct arguments instead.

```js
// Old way - removed
router.addRoute(
        pattern : "/myroute",
        handler : "myHandler",
        action : "myAction",
        matchVariables : "id=1&name=test"
    )

// New way - use rc or prc
router.addRoute(
        pattern : "/myroute",
        handler : "myHandler",
        action : "myAction",
        rc : { id = 1, name = "test" }
    )
```

### Router.setFullRewrites() Removed

The `setFullRewrites()` method has been removed from the Router. This method was used to enable/disable SES URL rewrites but is no longer needed as SES routing is handled automatically.

### InterceptorService.processState() Removed

The `processState()` method has been removed from the `InterceptorService` ([COLDBOX-1358](https://ortussolutions.atlassian.net/browse/COLDBOX-1358)). This method was deprecated and has now been completely removed.

**Migration:** Use the `announce()` method instead.

```js
// Old way - removed
interceptorService.processState( "myEvent", data );

// New way - use announce()
interceptorService.announce( "myEvent", data );
```

## Deprecations

The following methods were deprecated in ColdBox 7 and will be removed in ColdBox 9.

### Super Type Methods

* `renderView()` -> use `view()`
* `renderLayout()` -> use `layout()`
* `renderExternalView()` -> use `externalView()`
* `announceInterception()` -> use `announce()`
* `populateModel()` -> use `populate()`

### ScheduledExecutor

* `newSchedule()` -> use `newTask()`

## AI-Assisted Upgrade Instructions

The following instructions are designed to help AI assistants inspect and automatically upgrade ColdBox applications from version 7 to version 8.

### Code Inspection Patterns

When analyzing ColdBox applications for upgrade compatibility, check for these patterns:

#### 1. Cache Provider Interface Usage

Look for implementations extending deprecated interfaces:

```javascript
// Search for these deprecated patterns:
- extends="coldbox.system.cache.ICacheProvider"
- extends="coldbox.system.cache.IStats"
```

#### 2. BeanPopulator Usage

```javascript
// Search for BeanPopulator instantiation or injection:
- new coldbox.system.core.dynamic.BeanPopulator()
- property name="beanPopulator" inject="BeanPopulator"
- wirebox.getInstance( "BeanPopulator" )
```

#### 3. Client Flash Scope Usage

```javascript
// Search for client flash scope references:
- flash.setFlash()
- flash.getFlash()
- flashScope="client"
- setNextEvent( flashScope="client" )
```

#### 4. Environment Method Usage

```javascript
// Search for deprecated util methods:
- getSystemSetting()
- getSystemProperty()
- getEnv()
// When found in non-Env delegate contexts
```

#### 5. Deprecated Binder Methods

```javascript
// In WireBox binders, search for:
- .getProperty( default=
- .getCacheBoxConfig()
```

#### 6. RequestContext SES Methods

```javascript
// Search for these method calls:
- event.isSES()
- event.setSESEnabled()
```

#### 7. Router Removed Methods

```javascript
// Search for these router method calls:
- router.getModulesRoutingTable()
- router.includeRoutes()
- router.with()
- router.endWith()
- router.setFullRewrites()
- matchVariables argument in addRoute()
```

#### 8. InterceptorService Removed Methods

```javascript
// Search for deprecated InterceptorService method calls:
- interceptorService.processState()
- processState() // when called within interceptor context
```

#### 9. Super Type Method Usage

```javascript
// Search for these method calls in handlers/interceptors:
- renderView()
- renderLayout()
- renderExternalView()
- announceInterception()
- populateModel()
- newSchedule() // in ScheduledExecutor context
```

### Automated Replacement Rules

Apply these replacements when upgrading code:

#### Cache Provider Updates

```javascript
// Replace:
extends="coldbox.system.cache.ICacheProvider"
// With:
extends="coldbox.system.cache.providers.ICacheProvider"

// Replace:
extends="coldbox.system.cache.IStats"
// With:
extends="coldbox.system.cache.util.IStats"
```

#### BeanPopulator to ObjectPopulator

```javascript
// Replace all instances of:
BeanPopulator
// With:
ObjectPopulator

// Replace injection:
inject="BeanPopulator"
// With:
inject="ObjectPopulator"
```

#### Environment Method Delegation

```javascript
// Replace utility method calls with Env delegate:
getSystemSetting( "key", "default" )
// With:
new coldbox.system.core.delegates.Env().getSystemSetting( "key", "default" )

// Or inject the delegate:
property name="env" inject="coldbox.system.core.delegates.Env";
// Then use:
env.getSystemSetting( "key", "default" )
```

#### Binder Method Updates

```javascript
// Replace:
.getProperty( key, default=value )
// With:
.getProperty( key, defaultValue=value )

// Replace:
.getCacheBoxConfig()
// With:
.getCacheBox()
```

#### Router Method Updates

```javascript
// Replace:
router.getModulesRoutingTable()
// With:
router.getModuleRoutingTable()

// Replace with() and endWith() patterns:
router.with( "api", function( route ) {
    route.get( "/users", "users.index" );
} ).endWith();
// With:
router.group( { prefix: "api" }, function( route ) {
    route.get( "/users", "users.index" );
} );

// Replace matchVariables:
router.addRoute(
    pattern="/route",
    handler="handler",
    matchVariables="id=1&name=test"
)
// With:
router.addRoute(
    pattern="/route",
    handler="handler",
    rc={ id=1, name="test" }
)
```

#### InterceptorService Method Updates

```javascript
// Replace:
interceptorService.processState( "eventName", data )
// With:
interceptorService.announce( "eventName", data )

// Replace:
processState( "eventName", data )
// With:
announce( "eventName", data )
```

#### Super Type Method Updates

```javascript
// Replace deprecated methods:
renderView() -> view()
renderLayout() -> layout()
renderExternalView() -> externalView()
announceInterception() -> announce()
populateModel() -> populate()
newSchedule() -> newTask()
```

#### Client Flash Removal

```javascript
// Remove or replace client flash usage:
// Replace with session or cachebox alternatives
setNextEvent( url="page", flashScope="client" )
// With:
setNextEvent( url="page" ) // uses session by default
// Or:
setNextEvent( url="page", flashScope="session" )
```

### Upgrade Validation

After applying automated changes, verify:

1. **Engine Compatibility**: Ensure minimum ColdFusion 2021+ or Lucee 5.3+
2. **Test Coverage**: Run existing test suites to validate functionality
3. **Cache Providers**: Test custom cache provider implementations
4. **Module Compatibility**: Verify all modules work with updated router methods
5. **Environment Variables**: Ensure environment delegate usage works correctly

### Manual Review Required

These patterns require manual developer review:

* **Custom cache providers** extending old interfaces need logic review
* **Complex routing configurations** using deprecated methods may need restructuring
* **Client flash scope usage** requires architectural decisions for replacement
* **Environment method usage** in performance-critical code may benefit from injection optimization

### Completion Checklist

* [ ] All deprecated interface extensions updated
* [ ] BeanPopulator references changed to ObjectPopulator
* [ ] Client flash scope usage removed or replaced
* [ ] Environment method calls updated to use Env delegate
* [ ] Binder method calls updated (default -> defaultValue)
* [ ] RequestContext SES methods removed
* [ ] Router deprecated methods updated
* [ ] Super type method calls updated to new names
* [ ] All tests passing
* [ ] Manual review completed for complex patterns
