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
 convention has been removed and all references to plugin injection or DSL's are gone. You must now place all your plugins in your
**models** directory and request them via `getInstance() or
getModel()` calls.

Plugins are an old ColdBox convention but their baggage doesn't really serve a purpose now that we have modules for easy packaging of libraries and WireBox for easy creation of CFCs. Neither of those existed back when Plugins were birthed. It's time to say goodbye to the concept of plugins, but all their functionality will still be here, just with a slightly different (and more standardized) way of creating them.

```javascript
// old
getPlugin("MyPlugin")
property name="myPlugin" inject="coldbox:plugin:myPlugin";

// new
getInstance( "MyPlugin@module" ) or getModel( "MyPlugin@module" )
property name="myPlugin" inject="model:myPlugin@module";
```

### Plugin Base Class
With the removal of plugins, `coldbox.system.Plugin` no longer exists. If you have custom-written plugins that used some of the convenience variables such as controller, logbox, or wirebox that came from this base class, you'll need to inject them using the appropriate injection DSL. If you were using any of the convenience methods such as `getRequestContext()` or `getRequestCollection()` should be delegated to the appropriate service or the ColdBox controller.

Any variables or methods related to `instance.pluginName, instance.pluginVersion,` etc serve no purpose now and can be removed from the code.

## New Core Modules

All internal plugins and lots of functionality has been refactored out
of the ColdBox Core and into standalone Modules. All of them are in
[ForgeBox](http://www.coldbox.org/forgebox) and have their own Git repositories. Also, all of them are
installable via [CommandBox](http://www.ortussolutions.com/products/commandbox); Our ColdFusion (CFML) CLI, and Package
Manager.

You can find each of these modules in the main [GitHub repo](https://github.com/ColdBox). Check out the instructions.md files inside the root of each module with additional information on configuration and installation.


### Storages
- Session Storage Plugin
- Application Storage Plugin
- Client Storage Plugin
- Cluster Storage Plugin
- Cookie Storage Plugin

All of these plugins have been refactored into the `cbstorages` module.

```
box install cbstorages
```

Instead of using:

```js
getPlugin( 'SessionStorage' )
getPlugin( 'ApplicationStorage' )
getPlugin( 'CookieStorage' )
etc...
```

You will instead call

```js
getInstance( 'sessionStorage@cbstorages' )
getInstance( 'applicationStorage@cbstorages' )
getInstance( 'cookieStorage@cbstorages' )
etc...
```

>**Note** : The API for the actual storage CFCs is the same.


###MessageBox

```
box install cbmessagebox
```

This module replaces the MessageBox plugin and registers the following mapping in WireBox: `messagebox@cbmessagebox`.

### AntiSamy
```
box install cbantisamy
```
This module replaces the AntiSamy plugin and registers the following mapping in WireBox: `antisamy@cbantisamy`.

### MailServies

```
box install cbmailservices
```

This module replaces the MailService plugin and registers the following mapping in WireBox: `mailService@cbmailservices`.

### Validation

```
box install cbvalidation
```

This module replaces the previously-inbuilt validation functionality of ColdBox and the validator Plugin. The `ValidationManager` is still available under this WireBox mapping: `ValidationManager@cbvalidation`. It also gives you the following methods in every handler, view, layout, etc:

* `validateModel()`
* `getValidationManager()`


### ColdBox Debugger
If you want to use the ColdBox debugger, you'll need to install the `cbdebugger` module. Another benifit of this is you can omit this module in production so there's no security concerns with it getting turned on. To install as a development dependency in CommandBox, use the `--saveDev` flag.

```
box install cbdebugger --saveDev
```

Debugger settings can still be set in the main `ColdBox.cfc` config in a `debugger` struct. The `DebuggerService` and `Timer` are still available via the following WireBox mappings and can be retrieved via `getInstance()` or property injections.

* `debuggerService@cbdebugger`
* `timer@cbdebugger`


###JavaLoader

```
box install cbjavaloader
```

This module replaces the JavaLoader plugin and registers the following mapping in WireBox: `loader@cbjavaloader`.

###i18n

```
box install cbi18n
```
This module us a combination of both the `ResourceBundle` plugin and the `i18n` plugin rolled together now and represented by the following two WireBox mappings:

* `i18n@cbi18n`
* `resourceService@cbi18n`

This module also adds the following methods into your handlers, views, layouts, etc:

* `getFWLocale()`
* `setFWLocale()`
* `getResource()`

###ORM
```
box install cborm
```

This module brings you all the ORM virtual services that are in ColdBox 3.x and replaces the `ORMService` Plugin, but note that the component paths have been updated. Instead of starting with `coldbox.system` they start with `cborm`.

| Old Path | New Path |
| -- | -- |
| coldbox.system.orm.hibernate.VirtualEntityService | cborm.models.VirtualEntityService |



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