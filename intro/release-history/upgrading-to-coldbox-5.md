# Upgrading to ColdBox 6

The major compatibility issues will be covered as well as how to smoothly upgrade to this release from previous ColdBox versions. You can also check out the [What's New](whats-new-with-6.0.0.md) guide to give you a full overview of the changes.

## Luce 4.5 Support Dropped

Lucee 4.5 support has been dropped

## ColdFusion 11 Support Dropped

ColdFusion 11 support has been dropped. Adobe doesn't support them anymore, so neither do we.

## Breaking Changes

The following settings have been changed and altering behavior:

* `coldbox.autoMapModels` is now defaulted to **true**
* `coldbox.onInvalidEvent` has been REMOVED in preference to `coldbox.invalidEventHandler`
* `coldbox.jsonPayloadToRC` is now defaulted to **true**

## System Path Changes

* Default Bug Report Files are now located in `/coldbox/system/exceptions/`. Previously `/coldbox/system/includes/`

