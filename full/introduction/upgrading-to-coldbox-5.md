# Upgrading to ColdBox 5

The major compatibility issues will be covered as well as how to smoothly
upgrade to this release from previous ColdBox versions. You can also
check out the [What's New](whats_new_with_500.md) guide to give you a full
overview of the changes.

## ColdFusion 9-10 Support Dropped

ColdFusion 9-10 support has been dropped.  Adobe doesn't support them anymore, so do we.

## Datasources Configuration Dropped

The `datasources` configuration setting directive has been dropped in favor of just leveraging the `settings` directive.  Just move your datasource metadata into the `settings` struct and reference it using the settings DSL.

**Previous**
```js
property name="dsn" inject="coldbox:datasource:mydsn"
```

**New**
```js
property name="dsn" inject="coldbox:settings:mydsn"
```


