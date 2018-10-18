# Upgrading to ColdBox 5

The major compatibility issues will be covered as well as how to smoothly upgrade to this release from previous ColdBox versions. You can also check out the [What's New](https://coldbox.ortusbooks.com/intro/introduction/whats-new-with-5.0.0) guide to give you a full overview of the changes.

## `event.getCurrentHandler()` returns different results

In ColdBox 4, calling `getCurrentHandler()` would return the module if it existed:

```text
module:handler
```

In ColdBox 5, we correctly remove the **module** name so it can be retrieved via `getCurrentModule()` instead.

```text
handler
```

## `setNextEvent()` deprecated in favor of `relocate()`

The `setNextEvent()` method has been renamed to `relocate()` to better adjust to modern times. This deprecation will be removed in future versions of ColdBox.

## Modules AutoReload deprecated

The modules `autoReload` flag has been deprecated. This causes more headaches than anything. If you want changes reflected, reinit the framework.

## `onInvalidEvent` renamed to `invalidEventHandler`

The ColdBox construct setting `onInvalidEvent` has been renamed now to `invalidEventHandler`. This will be removed in future versions of ColdBox.

## `setAutoReload()` Removed

The `setAutoReload()` flag in the SES interceptor has been removed. It is evil I tell you. If you want your routing to be re-read, then reinit the framework.

## BuildLink LinkTo Argument Deprecated

The `buildLink()` method had the argument `linkTo` to denote the event or route to link to. This was verbose, so we shortened it to `to`:

```text
<!-- Before -->
<a href="#event.buildLink( linkTo="/about/us" )#">About Us</a>

<!-- After -->
<a href="#event.buildLink( to="/about/us" )#">About Us</a>
```

## Railo Support Dropped

Railo support dropped. Any classes that started with the word `Railo` need to be changed to `Lucee` especially on the CacheBox providers.

## ColdFusion 9-10 Support Dropped

ColdFusion 9-10 support has been dropped. Adobe doesn't support them anymore, so neither do we.

## Datasources Configuration Dropped

The `datasources` configuration setting directive has been dropped in favor of just leveraging the `settings` directive. Just move your datasource metadata into the `settings` struct and reference it using the settings DSL.

**Previous**

```javascript
// Datasource definitions
datasources = {
    mydsn = {
        type = "mysql",
        username = "luis",
        name = "mydsn"
    }
}
```

```text
// Injections
property name="dsn" inject="coldbox:datasource:mydsn"
```

**New**

```javascript
// Settings
settings = {
    mydsn = {
        type = "mysql",
        username = "luis",
        name = "mydsn"
    }
}
```

```text
// Injections

property name="dsn" inject="coldbox:setting:mydsn"
```

## DSL Builder Interface Update

The WireBox interface: `coldbox.system.ioc.dsl.IDSLBuilder` has changed in ColdBox 5, so if you are implementing your own DSLs, then you must update it like so:

{% code-tabs %}
{% code-tabs-item title="Old DSL Builder, notice the output attribute" %}
```javascript
public any function init( required any injector ) output="false"{ 
public any function process( required definition, targetObject ) output="false"{
```
{% endcode-tabs-item %}
{% endcode-tabs %}

In ColdBox 5 we removed the output attribute:

{% code-tabs %}
{% code-tabs-item title="New DSL Builder" %}
```javascript
public any function init( required any injector ) { 
public any function process( required definition, targetObject ) {
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
Ticket Reference: [https://ortussolutions.atlassian.net/browse/COLDBOX-697](https://ortussolutions.atlassian.net/browse/COLDBOX-697)
{% endhint %}

