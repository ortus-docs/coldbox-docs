# Upgrading to ColdBox 6

The major compatibility issues will be covered as well as how to smoothly upgrade to this release from previous ColdBox versions. You can also check out the [What's New](whats-new-with-6.0.0.md) guide to give you a full overview of the changes.

## Lucee 4.5 Support Dropped

Lucee 4.5 support has been dropped.

## ColdFusion 11 Support Dropped

ColdFusion 11 support has been dropped. Adobe doesn't support them anymore, so neither do we.

## Setting Changes

The following settings have been changed and altering behavior:

* `coldbox.autoMapModels` is now defaulted to **true**
  * **Which means that all your models will be MAPPED for you. If you have specific mappers in your config/WireBox.cfc make sure they override the mapping or turn this setting false.**
* `coldbox.onInvalidEvent` has been **REMOVED** in preference to `coldbox.invalidEventHandler`
* `coldbox.jsonPayloadToRC` is now defaulted to **true**

## SES Interceptor Removed

The `SES` interceptor has finally been removed. You can now remove it from your interceptor declarations. If you are relying on the SES interceptor for routing, then you will need to access the `RoutingService` via the following injection methods or retrieval methods:

```javascript
controller.getRoutingService()

property name="routingService" inject="coldbox:routingService"
```

## **Method Changes**

### **WireBox Provider `get()` method to `$get()`**

All WireBox providers now implement the new interface which has changed the method of `get()` to `$get()` to avoid proxying to methods that already implement a `get()` method.  So if you are using the `get()` method just update it to the new `$get()` method.

```javascript
property name="userService" inject="provider:UserService";

function getData(){
  return userService.$get().search();
}
```

### **getSetting\(\)**

The `getSetting()` method does NOT include a `fwSetting` boolean argument anymore.  You can now use the `getColdBoxSetting()` method instead.

```javascript
getSetting( "version", true ) ==> getColdBoxSetting( "version" )
```

### announceInterception\( state, interceptData \) =&gt; announce\( state, data \)

This method has now been deprecated in favor of its shorthand `announce().`  This method will still work but it will be removed in the next major version. So just rename it now. Also note that the `interceptData` has now changed to just `data`

```javascript
announce( state, data )
```

### processState\( state, interceptData \) =&gt; announce\( state, data \)

This method was used in the event manager and interceptor service and has been marked for deprecation.  Please use the method `announce()` instead.  Which is also a consistency in naming now.

## `setNextEvent()` Removed

The method `setNextEvent()` has been removed in favor of `relocate().` We had deprecated this method in ColdBox 5.

## **Interceptor Arguments: interceptData =&gt; data**

All interceptors receive arguments when listening, we have renamed the `interceptData` to just `data`.  The old approach still works but it is marked as deprecated.  So just rename it to `data`

```javascript
function preProcess( event, data, buffer, rc, prc )

## instead of 

function preProcess( event, interceptData, buffer, rc, prc )
```

## System Path Changes

* Default Bug Report Files are now located in `/coldbox/system/exceptions/`. Previously `/coldbox/system/includes/`

So make sure you update your `CustomErrorTemplate` path to this new path:

```javascript
coldbox.customErrorTemplate = "/coldbox/system/includes/BugReport.cfm";
// to
coldbox.customErrorTemplate = "/coldbox/system/exceptions/BugReport.cfm";
// Or use our new template
coldbox.customErrorTemplate = "/coldbox/system/exceptions/Whoops.cfm";
```

## Rendering Changes

The entire rendering mechanisims in ColdBox 6 have changed.  We have retained backwards compatibility but there might be some loopholes that worked before that won't work now.  Basically, the renderer is a singleton and each view renders in isolation. Meaning if a view sets a variable NO OTHER view will have access to it.

