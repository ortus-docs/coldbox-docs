# Upgrading to ColdBox 6

The major compatibility issues will be covered as well as how to smoothly upgrade to this release from previous ColdBox versions. You can also check out the [What's New](whats-new-with-6.0.0.md) guide to give you a full overview of the changes.

## Luce 4.5 Support Dropped

Lucee 4.5 support has been dropped

## ColdFusion 11 Support Dropped

ColdFusion 11 support has been dropped. Adobe doesn't support them anymore, so neither do we.

## Setting Changes

The following settings have been changed and altering behavior:

* `coldbox.autoMapModels` is now defaulted to **true**
* `coldbox.onInvalidEvent` has been REMOVED in preference to `coldbox.invalidEventHandler`
* `coldbox.jsonPayloadToRC` is now defaulted to **true**

## **Method Changes**

The `getSetting()` method does NOT include a `fwSetting` boolean argument anymore.  You can now use the `getColdBoxSetting()` method instead.

```javascript
getSetting( "version", true ) ==> getColdBoxSetting( "version" )
```

## System Path Changes

* Default Bug Report Files are now located in `/coldbox/system/exceptions/`. Previously `/coldbox/system/includes/`

## Rendering Changes

The entire rendering mechanisims in ColdBox 6 have changed.  We have retained backwards compatibility but there might be some loopholes that worked before that won't work now.  Basically, the renderer is a singleton and each view renders in isolation. Meaning if a view sets a variable NO OTHER view will have access to it.

