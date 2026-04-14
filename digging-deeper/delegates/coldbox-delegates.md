---
description: ColdBox-specific delegates for rendering, routing, settings, interception, and more
---

# ColdBox Delegates

ColdBox registers its own set of delegates under the **`@cbDelegates`** namespace. These are available whenever your application runs inside ColdBox and give any WireBox-managed object direct access to framework capabilities without needing to extend a base class.

{% hint style="success" %}
`@cbDelegates` are registered by ColdBox's `LoaderService` at startup via:
`binder.mapDirectory( packagePath="coldbox.system.web.delegates", namespace="@cbDelegates" )`
{% endhint %}

## Available ColdBox Delegates

| WireBox ID                  | Description                                                        |
| --------------------------- | ------------------------------------------------------------------ |
| `AppModes@cbDelegates`      | Check the current application mode                                 |
| `Interceptor@cbDelegates`   | Announce and listen to ColdBox interception events                 |
| `Locators@cbDelegates`      | Locate files and directories in the ColdBox application            |
| `Population@cbDelegates`    | Populate objects from the request collection, JSON, XML, or query  |
| `Rendering@cbDelegates`     | Render views, layouts, and external views                          |
| `Routable@cbDelegates`      | Build links, routes, and access base URL helpers                   |
| `Settings@cbDelegates`      | Read and write ColdBox and module settings                         |

---

## AppModes

**WireBox ID:** `AppModes@cbDelegates`\
**Class:** `coldbox.system.web.delegates.AppModes`

Delegates mode-detection methods from the ColdBox `Controller`. Useful for conditionally executing logic based on the current runtime environment.

| Method            | Return  | Description                                                         |
| ----------------- | ------- | ------------------------------------------------------------------- |
| `inDebugMode()`   | boolean | Returns `true` when ColdBox debug mode is active.                   |
| `isDevelopment()` | boolean | Returns `true` when the ColdBox environment is `development`.       |
| `isProduction()`  | boolean | Returns `true` when the ColdBox environment is `production`.        |
| `isTesting()`     | boolean | Returns `true` when the ColdBox environment is `testing`.           |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="AppModes@cbDelegates" {

    function getConfig() {
        var config = loadBaseConfig()

        when( isDevelopment(), () => {
            config.logLevel = "DEBUG"
            config.cacheEnabled = false
        } )

        when( isProduction(), () => {
            config.logLevel = "ERROR"
            config.cacheEnabled = true
        } )

        return config
    }

    function render() {
        if ( inDebugMode() ) {
            return renderDebugInfo()
        }
        return renderNormal()
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="AppModes@cbDelegates" {

    function getConfig() {
        var config = loadBaseConfig()

        if ( isDevelopment() ) {
            config.logLevel    = "DEBUG"
            config.cacheEnabled = false
        }

        if ( isProduction() ) {
            config.logLevel    = "ERROR"
            config.cacheEnabled = true
        }

        return config
    }

    function render() {
        if ( inDebugMode() ) {
            return renderDebugInfo()
        }
        return renderNormal()
    }

}
```
{% endtab %}
{% endtabs %}

---

## Interceptor

**WireBox ID:** `Interceptor@cbDelegates`\
**Class:** `coldbox.system.web.delegates.Interceptor`

Delegates interception methods from the ColdBox `InterceptorService`. Allows any object to participate in the interception pipeline without injecting the service directly.

| Method                             | Description                                                                                        |
| ---------------------------------- | -------------------------------------------------------------------------------------------------- |
| `announce( state, data, ... )`     | Announces a ColdBox interception event, optionally passing a data struct to all listeners.         |
| `listen( state, interceptor, ... )`| Dynamically registers a closure/lambda as a listener for the given interception point at runtime.  |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Interceptor@cbDelegates" {

    function save( required entity ) {
        announce( "preEntitySave", { entity : entity } )

        // ... save logic ...

        announce( "postEntitySave", { entity : entity } )
        return entity
    }

    function watchForLogin() {
        // Register a closure listener dynamically
        listen( "onUserLogin", ( data ) => {
            logBox.getRootLogger().info( "User logged in: #data.user.getEmail()#" )
        } )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Interceptor@cbDelegates" {

    function save( required entity ) {
        announce( "preEntitySave", { entity : entity } )

        // ... save logic ...

        announce( "postEntitySave", { entity : entity } )
        return entity
    }

    function watchForLogin() {
        // Register a closure listener dynamically
        listen( "onUserLogin", function( data ) {
            logBox.getRootLogger().info( "User logged in: #data.user.getEmail()#" )
        } )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Locators

**WireBox ID:** `Locators@cbDelegates`\
**Class:** `coldbox.system.web.delegates.Locators`

Delegates file and directory location methods from the ColdBox `Controller`. Useful for resolving application-relative paths without hard-coding conventions.

| Method                                      | Description                                                                  |
| ------------------------------------------- | ---------------------------------------------------------------------------- |
| `locateFilePath( path )`                    | Returns the absolute disk path for a file within the ColdBox application.    |
| `locateDirectoryPath( path )`               | Returns the absolute disk path for a directory within the ColdBox application.|

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Locators@cbDelegates" {

    function loadTemplate( required string name ) {
        var filePath = locateFilePath( "includes/templates/#name#.txt" )
        if ( !fileExists( filePath ) ) {
            throw( type: "TemplateNotFound", message: "Template [#name#] not found" )
        }
        return fileRead( filePath )
    }

    function getUploadDirectory() {
        return locateDirectoryPath( "resources/uploads" )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Locators@cbDelegates" {

    function loadTemplate( required string name ) {
        var filePath = locateFilePath( "includes/templates/#name#.txt" )
        if ( !fileExists( filePath ) ) {
            throw( type="TemplateNotFound", message="Template [#name#] not found" )
        }
        return fileRead( filePath )
    }

    function getUploadDirectory() {
        return locateDirectoryPath( "resources/uploads" )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Population

**WireBox ID:** `Population@cbDelegates`\
**Class:** `coldbox.system.web.delegates.Population`

An enhanced population delegate that integrates with ColdBox's request context. When `memento` is not provided, it **automatically defaults to the current HTTP request collection** (`rc`).

{% hint style="warning" %}
This is different from `Population@coreDelegates`. The ColdBox version defaults `memento` to the **current request collection** (`rc`) when no `memento` argument is passed. Use this version in handlers and request-aware services; use `Population@coreDelegates` in pure domain models.
{% endhint %}

| Method               | Arguments                                        | Description                                                                                      |
| -------------------- | ------------------------------------------------ | ------------------------------------------------------------------------------------------------ |
| `populate( model, ... )` | `model` — WireBox ID string **or** an existing object instance | Populates `model` from **the request collection** (default), or a passed `memento`, `jsonstring`, `xml`, or `qry`. Returns the populated instance. |

**Common optional arguments:**

| Argument                 | Default | Description                                                 |
| ------------------------ | ------- | ----------------------------------------------------------- |
| `memento`                | `rc`    | Explicit struct to populate from (overrides request collection) |
| `scope`                  | `""`    | Use scope injection instead of setter injection             |
| `trustedSetter`          | `false` | Call setters without checking they exist                    |
| `include`                | `""`    | Comma-delimited keys to include exclusively                 |
| `exclude`                | `""`    | Comma-delimited keys to exclude                             |
| `ignoreEmpty`            | `false` | Skip empty values                                           |
| `nullEmptyInclude`       | `""`    | Keys to `null` when empty                                   |
| `nullEmptyExclude`       | `""`    | Keys to NOT `null` when empty                               |
| `composeRelationships`   | `false` | Automatically compose object relationships                  |
| `jsonstring`             |         | Populate from a JSON string                                 |
| `xml`                    |         | Populate from an XML string                                 |
| `qry`                    |         | Populate from a query row                                   |
| `ignoreTargetLists`      | `false` | Ignore population include/exclude metadata on the target    |

{% tabs %}
{% tab title="BoxLang" %}
```bx
// In a handler — rc is automatically the source when no memento is given
class delegates="Population@cbDelegates" {

    function save( event, rc, prc ) {
        // Populates the User model from the current request collection
        var user = populate( "User" )

        // Explicit struct override
        var user2 = populate(
            model       : getInstance( "User" ),
            memento     : rc,
            ignoreEmpty : true,
            exclude     : "password,confirmPassword"
        )

        // Populate from JSON body
        var dto = populate(
            model      : "UserDTO",
            jsonstring : toString( getHttpRequestData().content )
        )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
// In a handler — rc is automatically the source when no memento is given
component delegates="Population@cbDelegates" {

    function save( event, rc, prc ) {
        // Populates the User model from the current request collection
        var user = populate( "User" )

        // Explicit struct override
        var user2 = populate(
            model       = getInstance( "User" ),
            memento     = rc,
            ignoreEmpty = true,
            exclude     = "password,confirmPassword"
        )

        // Populate from JSON body
        var dto = populate(
            model      = "UserDTO",
            jsonstring = toString( getHttpRequestData().content )
        )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Rendering

**WireBox ID:** `Rendering@cbDelegates`\
**Class:** `coldbox.system.web.delegates.Rendering`

Delegates rendering methods from the ColdBox `Renderer`. Allows any object — schedulers, services, models — to render views and layouts without injecting the renderer directly.

| Method                              | Description                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------- |
| `view( view, args, ... )`           | Renders a view and returns the HTML string.                                              |
| `layout( layout, args, ... )`       | Renders a layout and returns the HTML string.                                            |
| `externalView( view, args, ... )`   | Renders a view from an external location (outside the standard `views/` convention).    |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Rendering@cbDelegates" {

    function buildEmailBody( required struct user ) {
        // Render a view and capture it as a string for email sending
        return view( "emails/welcome", { user : user } )
    }

    function buildReport( required struct data ) {
        return layout( "pdf", { content : view( "reports/summary", data ) } )
    }

    function renderPlugin( required string path ) {
        return externalView( "/plugins/#path#/views/widget" )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Rendering@cbDelegates" {

    function buildEmailBody( required struct user ) {
        // Render a view and capture it as a string for email sending
        return view( "emails/welcome", { user : user } )
    }

    function buildReport( required struct data ) {
        return layout( "pdf", { content : view( "reports/summary", data ) } )
    }

    function renderPlugin( required string path ) {
        return externalView( "/plugins/#path#/views/widget" )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Routable

**WireBox ID:** `Routable@cbDelegates`\
**Class:** `coldbox.system.web.delegates.Routable`

Delegates routing and URL-building methods from the ColdBox `RequestContext`. Useful in services and models that need to generate links without having the request context injected.

| Method                          | Description                                                                          |
| ------------------------------- | ------------------------------------------------------------------------------------ |
| `buildLink( linkTo, ... )`      | Builds a framework URL for the given event or route.                                 |
| `route( name, params, ... )`    | Builds a URL using a named route.                                                    |
| `getHTMLBaseURL()`              | Returns the HTML base URL of the application.                                        |
| `getHTMLBasePath()`             | Returns the HTML base path of the application.                                       |
| `getSESBaseURL()`               | Returns the SES-enabled base URL.                                                    |
| `getSESBasePath()`              | Returns the SES-enabled base path.                                                   |
| `getPath()`                     | Returns the current request path.                                                    |
| `getUrl()`                      | Returns the current full request URL.                                                |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Routable@cbDelegates" {

    function getProfileUrl( required string username ) {
        return buildLink( "user.profile", { username : username } )
    }

    function getAvatarUrl( required string username ) {
        return getHTMLBaseURL() & "/assets/avatars/#username#.png"
    }

    function getApiLink( required string resource ) {
        return route( "api.#resource#" )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Routable@cbDelegates" {

    function getProfileUrl( required string username ) {
        return buildLink( "user.profile", { username : username } )
    }

    function getAvatarUrl( required string username ) {
        return getHTMLBaseURL() & "/assets/avatars/#username#.png"
    }

    function getApiLink( required string resource ) {
        return route( "api.#resource#" )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Settings

**WireBox ID:** `Settings@cbDelegates`\
**Class:** `coldbox.system.web.delegates.Settings`

Delegates settings access methods from the ColdBox `Controller`. Provides read/write access to application settings and module configuration.

| Method                                        | Description                                                                            |
| --------------------------------------------- | -------------------------------------------------------------------------------------- |
| `getSetting( name, defaultValue )`            | Returns an application setting by name. Throws if missing and no default given.        |
| `getColdBoxSetting( name, defaultValue )`     | Returns a ColdBox internal framework setting by name.                                  |
| `settingExists( name )`                       | Returns `true` if the application setting exists.                                      |
| `setSetting( name, value )`                   | Sets or overrides an application setting at runtime.                                   |
| `getModuleSettings( module )`                 | Returns the settings struct for the given module.                                      |
| `getModuleConfig( module )`                   | Returns the full configuration struct for the given module.                            |
| `getUserSessionIdentifier()`                  | Returns the current user session identifier.                                           |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Settings@cbDelegates" {

    function getApiBaseUrl() {
        return getSetting( "apiBaseUrl", "https://api.example.com" )
    }

    function isFeatureEnabled( required string flag ) {
        var features = getSetting( "featureFlags", {} )
        return features.keyExists( flag ) && features[ flag ]
    }

    function getMailConfig() {
        // Get the cbmailservices module settings
        return getModuleSettings( "cbmailservices" )
    }

    function flagUser( required string userId ) {
        var flagged = getSetting( "flaggedUsers", [] )
        flagged.append( userId )
        setSetting( "flaggedUsers", flagged )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Settings@cbDelegates" {

    function getApiBaseUrl() {
        return getSetting( "apiBaseUrl", "https://api.example.com" )
    }

    function isFeatureEnabled( required string flag ) {
        var features = getSetting( "featureFlags", {} )
        return features.keyExists( flag ) && features[ flag ]
    }

    function getMailConfig() {
        // Get the cbmailservices module settings
        return getModuleSettings( "cbmailservices" )
    }

    function flagUser( required string userId ) {
        var flagged = getSetting( "flaggedUsers", [] )
        flagged.append( userId )
        setSetting( "flaggedUsers", flagged )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Combining Delegates

Delegates compose cleanly. A scheduler that needs to render emails, read settings, and announce events:

{% tabs %}
{% tab title="BoxLang" %}
```bx
class
    extends="coldbox.system.web.tasks.ColdBoxScheduledTask"
    delegates="Rendering@cbDelegates,
               Settings@cbDelegates,
               Interceptor@cbDelegates,
               Flow@coreDelegates" {

    function run() {
        var recipients = getSetting( "reportRecipients", [] )

        throwIf(
            recipients.isEmpty(),
            "SchedulerException",
            "No report recipients configured"
        )

        var html = view( "reports/daily" )

        announce( "onDailyReportReady", { html : html, recipients : recipients } )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component
    extends="coldbox.system.web.tasks.ColdBoxScheduledTask"
    delegates="Rendering@cbDelegates,
               Settings@cbDelegates,
               Interceptor@cbDelegates,
               Flow@coreDelegates" {

    function run() {
        var recipients = getSetting( "reportRecipients", [] )

        throwIf(
            recipients.isEmpty(),
            "SchedulerException",
            "No report recipients configured"
        )

        var html = view( "reports/daily" )

        announce( "onDailyReportReady", { html : html, recipients : recipients } )
    }

}
```
{% endtab %}
{% endtabs %}
