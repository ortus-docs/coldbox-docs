# Upgrading to ColdBox 5

The major compatibility issues will be covered as well as how to smoothly
upgrade to this release from previous ColdBox versions. You can also
check out the [What's New](whats_new_with_500.md) guide to give you a full
overview of the changes.


## BuildLink LinkTo Argument Dropped

The `buildLink()` method had the argument `linkTo` to denote the event or route to link to.  This was verbose, so we shortened it to `to`:

```html
// previous
<a href="#event.buildLink( linkTo="/about/us" )#">About Us</a>

// New
<a href="#event.buildLink( linkTo="/about/us" )#">About Us</a>
```

## Railo Support Dropped

Railo support dropped. Any classes that started with the word `Railo` need to be changed to `Lucee` especially on the CacheBox providers.

## ColdFusion 9-10 Support Dropped

ColdFusion 9-10 support has been dropped.  Adobe doesn't support them anymore, so do we.

## Datasources Configuration Dropped

The `datasources` configuration setting directive has been dropped in favor of just leveraging the `settings` directive.  Just move your datasource metadata into the `settings` struct and reference it using the settings DSL.

**Previous**
```js
// Datasource definitions
datasources = {
    mydsn = {
        type = "mysql",
        username = "luis",
        name = "mydsn"
    }
}

// Injections
property name="dsn" inject="coldbox:datasource:mydsn"
```

**New**
```js
// Settings
settings = {
    mydsn = {
        type = "mysql",
        username = "luis",
        name = "mydsn"
    }
}


// Injections

property name="dsn" inject="coldbox:settings:mydsn"
```


