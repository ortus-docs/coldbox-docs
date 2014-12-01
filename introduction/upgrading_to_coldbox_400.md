# Upgrading to ColdBox 4.0.0

The
major compatibility issues will be covered as well as how to smoothly
upgrade to this release from previous ColdBox versions. You can also
check out the [What's New](whats_new_with_400.md) guide to give you a full
overview of the changes.

## ColdFusion 8 Support Dropped

ColdFusion 8 support has been dropped.

## Async Loggers Dropped

The Asynchronous loggers in LogBox have been removed in preference to
the new `async` property that can be used in **any** logger. The
affected loggers are:

-   AsyncDBAppender -\> DBAppender
-   AsyncFileAppender -\> FileAppender
-   AsyncRollingFileAppender -\> RollingFileAppender

You can just declare each appender but add an `async=true`
property to each when declaring:

```javascript
logBox = {
    // Define Appenders
    appenders = {
        coldboxTracer = { class="coldbox.system.logging.appenders.ConsoleAppender",properties={async:true} }
    },
    // Root Logger
    root = { levelmax="INFO", appenders="*" },
    // Implicit Level Categories
    info = [ "coldbox.system" ]
};
```

## Plugins Deprecated

ColdBox Plugins have graduated to become just models. The **plugins**
convention has been removed and all references to plugin injection or
DSL's have been deprecated. You must now place all your plugins in your
**models** directory and request them via `getInstance() or
getModel()` calls.

```javascript
// old
getPlugin("MyPlugin")

// new
getInstance( "MyPlugin" ) or getModel( "MyPlugin" )
```

## New Core Modules

All internal plugins and lots of functionality has been refactored out
of the ColdBox Core and into standalone Modules. All of them are in
[ForgeBox](http://www.coldbox.org/forgebox) and have their own Git repositories. Also, all of them are
installable via [CommandBox][]; Our ColdFusion (CFML) CLI, and Package
Manager.

-   ColdBox Debugger

``` {.coldfusion}
box install cbdebugger
```

-   Storages

``` {.coldfusion}
box install coldbox-storages
```

-   Feeds

``` {.coldfusion}
box install feeds
```

-   Commons

``` {.coldfusion}
box install cbcommons
```

-   i18n

``` {.coldfusion}
box install i18n
```

-   ORM

``` {.coldfusion}
box install cborm
```

-   ioc

``` {.coldfusion}
box install ioc
```

-   JavaLoader

``` {.coldfusion}
box install javaloader
```

-   AntiSamy

``` {.coldfusion}
box install antisamy
```

-   MailServies

``` {.coldfusion}
box install mailservices
```

-   MessageBox

``` {.coldfusion}
box install messagebox
```

-   Soap

``` {.coldfusion}
box install soap
```

-   Security

``` {.coldfusion}
box install ColdBox-Security
```

-   Validation

``` {.coldfusion}
box install validation
```