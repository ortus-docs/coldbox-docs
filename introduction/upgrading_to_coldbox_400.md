# Upgrading to ColdBox 4.0.0

The
major compatibility issues will be covered as well as how to smoothly
upgrade to this release from previous ColdBox versions. You can also
check out the [What's New](whats_new_with_400.md) guide to give you a full
overview of the changes.

## ColdBox 3.x Compatibility Module
If you want to convert a larger ColdBox 3.x site over piece by piece, we have created a ColdBox Compat module you can install that will give you much of the 3.x functionality back including plugins, ColdBox OCM, `UDFLibraryFile` setting, and WireBox DSL namespaces. You quickly install this module using CommandBox with the following command:

```bash
box install cbcompat
```

For more information and a full list of features, visit the [ForgeBox page.](http://www.coldbox.org/forgebox/view/cbcompat)


## ColdFusion 8 Support Dropped

ColdFusion 8 support has been dropped.

## Application.cfc Bootstrap changed
The bootstrap CFC used by `Application.cfc` has been updated from `coldbox.system.Coldbox` to `coldbox.system.Bootstrap`. This is basically just a rename-- all other functionality is the same so a simple find/replace in your `Application.cfc` should fix it up. This is the first change you'll need to make and is mandatory.

Sample `Application.cfc` using inheritance

```js
// Old code
component extends='coldbox.system.Coldbox' { }

// New code
component extends='coldbox.system.Bootstrap' { }
```

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

## Plugins Removed

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
installable via [CommandBox](http://www.ortussolutions.com/products/commandbox); Our ColdFusion (CFML) CLI, and Package
Manager.

-   ColdBox Debugger

```bash
box install cbdebugger
```

-   Storages

```bash
box install coldbox-storages
```

-   Feeds

```bash
box install feeds
```

-   Commons

```bash
box install cbcommons
```

-   i18n

```bash
box install i18n
```

-   ORM

```bash
box install cborm
```

-   ioc

```bash
box install ioc
```

-   JavaLoader

```bash
box install javaloader
```

-   AntiSamy

```bash
box install antisamy
```

-   MailServies

```bash
box install mailservices
```

-   MessageBox

```bash
box install messagebox
```

-   Soap

```bash
box install soap
```

-   Security

```bash
box install ColdBox-Security
```

-   Validation

```bash
box install validation
```

## Model Convention

The **model** convention has been renamed to **models** to be consistent
with pluralization. So you must either rename your folder or use Custom
Conventions in your Configuration CFC.

## ColdBox OCM Dropped

References to `getColdboxOCM()` have been removed in preference to
`getCache()` calls.


##JSON Plugin Dropped

Placed in ForgeBox

## Validator Plugin Dropped

Removed from core

## Datasource Bean Dropped

The datasource bean has been droped in favor of flat structures. So
instead of getting a bean representing a datasource structure, you just
get the structure. So some old code like this:

```html
<!--- Dependencies --->
<cfproperty name="dsn" inject="coldbox:datasource:mydsn">

<!--- list --->
<cffunction name="list" output="false" access="public" returntype="query" hint="Return the contacts">
    <cfset var q = "">
    
    <cfquery name="q" datasource="#dsn.getName()#">
    SELECT * 
        FROM contacts
    ORDER BY name asc
    </cfquery>
    
    <cfreturn q>
    
</cffunction>
```

Would become this:

```html
<!--- Dependencies --->
<cfproperty name="dsn" inject="coldbox:datasource:mydsn">

<!--- list --->
<cffunction name="list" output="false" access="public" returntype="query" hint="Return the contacts">
    <cfset var q = "">
    
    <cfquery name="q" datasource="#dsn.name#">
    SELECT * 
        FROM contacts
    ORDER BY name asc
    </cfquery>
    
    <cfreturn q>
    
</cffunction>
```





```