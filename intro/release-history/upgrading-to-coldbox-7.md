---
description: The official ColdBox 7 upgrade guide
---

# Upgrading to ColdBox 7

The major compatibility issues will be covered, as well as how to upgrade to this release from previous ColdBox versions smoothly. You can also check out the [What's New](whats-new-with-7.0.0/) guide to give you a full overview of the changes.

## ColdFusion 2016 Support Dropped

ColdFusion 2016 support has been dropped. Adobe doesn't support them anymore, so neither do we.

## Hierarchical Injectors

All modules have their own injector now if you use the `this.moduleInjector = true` setting.  Meaning the concept of a global injector no longer exists. Therefore, there are some edge cases where certain types of code will not work in ColdBox 7.

### Testing Injector Creations

If you are creating your own WireBox injector in your tests and using integration testing, you will have Injector collisions.

```javascript
myInjector = new coldbox.system.ioc.Injector()
```

This actually affects **EVERY** version of ColdBox because the default behavior of instantiating an Injector like the code above is to put the Injector in application scope: `application.wirebox.` This means that the REAL injector in an integration test lives in `application.wirebox` will be overridden.  To avoid this collision, disable scope registration:

```javascript
myInjector = new coldbox.system.ioc.Injector( {
    scopeRegistration : { enabled : false }hj
} )
```

## Custom Wirebox DSLs

For those of you with custom wirebox DSLs, you'll need to update your DSL to match the new `process()` method signature:

```js

/**
 * Process an incoming DSL definition and produce an object with it
 *
 * @definition   The injection dsl definition structure to process. Keys: name, dsl
 * @targetObject The target object we are building the DSL dependency for. If empty, means we are just requesting building
 * @targetID     The target ID we are building this dependency for
 *
 * @return coldbox.system.ioc.dsl.IDSLBuilder
 */
function process( required definition, targetObject, targetID );
```

##

## Removals

### AnnounceInterception

The `announceInterception()` method [has been deprecated since ColdBox 6.0.0](https://coldbox.ortusbooks.com/v/v6.x/intro/release-history/whats-new-with-6.0.0#announceinterception-processstate-deprecated). You will need to refactor any uses of `announceInterception()` to use `announce()` instead.

### routes.cfm

The `routes.cfm` approach is now removed in ColdBox 7. You will need to migrate to the `Router.cfc` [approach](../../the-basics/routing/) in your application and/or modules.

### setUniqueURLs()

This setting was in charge of converting NON-SES Urls into SES URLs.  However, it was extremely error-prone and sometimes produced invalid URLs.  This is now completely removed and if the user wants to do this feature, they can use CommandBox or Nginx, or Apache rewrites.

### `jsonQueryFormat` Removed

The `jsonQueryFormat` argument for rendering data is now removed. We default to an array of structs format for queries as it is the only format that makes sense.



## Deprecations

### renderView(), renderLayout(), renderExternalView()

These methods have been deprecated since version 6. Please use the shorthand versions

* `view()`
* `layout()`
* `externalView()`

### Utility Environment Methods

The core utility methods on `env` and Java variables have been deprecated from the utility object and moved to the `Env` delegate. Here are the methods deprecated:

* `getSystemSetting()`
* `getSystemProperty()`
* `getEnv()`

So if you had code like this:

```javascript
// Deprecated
new coldbox.system.core.util.Util().getSystemSetting()
```

That will work, but it is deprecated now. You will have to move it to the delegate:

```javascript
new coldbox.system.core.delegates.Env().getSystemSetting()
```

If you were using them in your integration or unit tests, then you can use our shorthand methods:

```javascript
getEnv().getSystemSetting()
getEnv().getSystemProperty()
getEnv().getEnv()
```

### BeanPopulator Deprecated

The object `BeanPopulator` has been deprecated in favor of `ObjectPopulator`.



###

###
