# Upgrading to ColdBox 7

The major compatibility issues will be covered as well as how to smoothly upgrade to this release from previous ColdBox versions. You can also check out the [What's New](whats-new-with-7.0.0.md) guide to give you a full overview of the changes.

## ColdFusion 2016 Support Dropped

ColdFusion 2016 support has been dropped. Adobe doesn't support them anymore, so neither do we.

## Removals

### AnnounceInterception

The `announceInterception()` method [has been deprecated since ColdBox 6.0.0](https://coldbox.ortusbooks.com/v/v6.x/intro/release-history/whats-new-with-6.0.0#announceinterception-processstate-deprecated). You will need to refactor any uses of `announceInterception()` to use `announce()` instead.

### routes.cfm

The `routes.cfm` approach is now removed in ColdBox 7. You will need to migrate to the `Router.cfc` [approach](../../the-basics/routing/) in your application and/or modules.

### renderView(), renderLayout(), renderExternalView()

These methods are deprecated since version 6. Please use the shorthand versions

* `view()`
* `layout()`
* `externalView()`

### `jsonQueryFormat` Removed

The `jsonQueryFormat` argument for rendering data is now removed. We default to an array of structs format for queries as it is the only format that makes sense.

### Utility Environment Methods

The core utility methods on env and Java variables have been removed from the utility object and moved to the `Env` delegate.  Here are the methods removed:

* `getSystemSetting()`
* `getSystemProperty()`
* `getEnv()`

So if you had code like this:

```javascript
// Deprecated
new coldbox.system.core.util.Util().getSystemSetting()
```

That will break.  You will have to move it to the delegate:

```javascript
new coldbox.system.core.delegates.Env().getSystemSetting()
```

## Deprecations

### BeanPopulator Deprecated

The name `BeanPopulator` has been deprecated in favor of `ObjectPopulator`.
