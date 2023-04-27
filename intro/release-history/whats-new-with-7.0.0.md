---
description: Discover the power of ColdBox 7.0.0
---

# What's New With 7.0.0

ColdBox 7.0.0 is a major release for the ColdBox HMVC platform. It has some dramatic new features as we keep pushing for more modern and sustainable approaches to web development and tons of bug fixes and improvements.

We break down the major areas of development below, and you can also find the [full release notes](whats-new-with-7.0.0/release-notes.md) per library at the end.

## Engine Support

<figure><img src="../../.gitbook/assets/coldbox-engine-support.jpeg" alt=""><figcaption><p>ColdBox v7 Engine Support</p></figcaption></figure>

This release drops support for Adobe 2016 and adds support for Adobe 2023 and Lucee 6 (Beta).  Please note that there are still issues with Adobe 2023 as it is still in Beta.

## ColdBox CLI

We now have an official CLI for CommandBox, which lives outside CommandBox.  It will always be included with CommandBox, but it now has its own life cycles, and it will support each major version of ColdBox as well.

```bash
install coldbox-cli
coldbox --help
```

The new CLI has all the previous goodness but now also v7 support and many other great features like migration creation, API testing, and more.  You can find the source for the CLI here: [https://github.com/coldbox/coldbox-cli](https://github.com/coldbox/coldbox-cli)

{% embed url="https://github.com/coldbox/coldbox-cli" %}

## User Identifier Providers

In previous versions of ColdBox, it would auto-detect unique request identifiers for usage in Flash Ram, storages, etc., following this schema:

1. If we have `session` enabled, use the `jessionId` or `session` URL Token
2. If we have cookies enabled, use the `cfid/cftoken`
3. If we have in the `URL` the `cfid/cftoken`
4. Create a unique request-based tracking identifier: `cbUserTrackingId`

However, you can now decide what will be the unique identifier for requests, flash RAM, etc by providing it via a `coldbox.identifierProvider` as a closure/lambda in your `config/Coldbox.cfc`

```javascript
coldbox : {
    ...
    
    identifierProvider : () => {
        // My own logic to provide a unique tracking id
        return myTrackingID
    }
    
    ...

}
```

If this closure exists, ColdBox will use the return value as the unique identifier.  A new method has also been added to the ColdBox Controller so you can retrieve this value:

```javascript
controller.getUserSessionIdentifier()
```

The supertype as well so all handlers/layouts/views/interceptors can get the user identifier:

```javascript
function getUserSessionIdentifier()
```

## Module Enhancements

### Config Object Override

In ColdBox 7, you can now store the module configurations outside of the `config/Coldbox.cfc`. Especially in an application with many modules and many configs, the `modulesettings` would get really unruly and long.  Now you can bring separation.  This new convention will allow module override configurations to exist as their own configuration file within the application’s config folder.

```bash
config/modules/{moduleName}.cfc
```

The configuration CFC will have one `configure()` method that is expected to return a struct of configuration settings as you did before in the `moduleSettings`

```javascript
component{

    function configure(){
        return {
            key : value
        };
    }

}
```

#### Injections

Just like a `ModuleConfig` this configuration override also gets many injections:

```
* controller
* coldboxVersion
* appMapping
* moduleMapping
* modulePath
* logBox
* log
* wirebox
* binder
* cachebox
* getJavaSystem
* getSystemSetting
* getSystemProperty
* getEnv
* appRouter
* router
```

#### Env Support

This module configuration object will also inherit the `ModuleConfig.cfc` behavior that if you create methods with the same name as the **environment** you are on, it will execute it for you as well.

```javascript
function development( original ){
   // add overides to the original struct
}
```

Then you can change the `original` struct as you see fit for that environment.

### Config/Settings Awareness

You can now use the `{this}` placeholder in injections for module configurations-settings, and ColdBox will automatically replace it with the current module it's being injected in:

```javascript
property name="mySettings" inject="coldbox:moduleSettings:{this}"
property name="myConfig" inject="coldbox:moduleConfig:{this}"
```

This is a great way to keep your injections clean without adhering to the module name.

## Interceptor `listen()` Order

You can now use `listen( closure, point )` or `listen( point, closure)` when registering closure interception points.

```javascript
listen( ()=> log.info( "executed" ), "preProcess" )
// or
listen( "preProcess", ()=> log.info( "executed" ) )
```

## Delegates



## App Mode Helpers

ColdBox 7 introduces opinionated helpers to the `FrameworkSuperType` so you can determine if you are in three modes: production, development, and testing by looking at the `environment` setting:

```javascript
function isProduction()
function isDevelopment()
function isTesting()
```

| Mode                      | Environment              |
| ------------------------- | ------------------------ |
| `inProduction() == true`  | `production`             |
| `inTesting() == true`     | `development` or `local` |
| `inDevelopment() == true` | `testing`                |

{% hint style="info" %}
You can also find these methods in the `controller` object.
{% endhint %}

These super-type methods delegate to the ColdBox Controller.  So that means that if you needed to change their behavior, you could do so via a [Controller Decorator.](../../digging-deeper/controller-decorator.md) &#x20;

You can also use them via our new `AppModes@cbDelegates` delegate in any model:

```javascript
component delegate="AppModes@cbDelegates"{}
```

## Resource Route Names

When you register resourceful routes now, they will get assigned a name so you can use it for validation via `routeIs()` or for route link creation via `route()`

| Verb        | Route              | Event           | Route Name       |
| ----------- | ------------------ | --------------- | ---------------- |
| `GET`       | `/photos`          | `photos.index`  | `photos`         |
| `GET`       | `/photos/new`      | `photos.new`    | `photos.new`     |
| `POST`      | `/photos`          | `photos.create` | `photos`         |
| `GET`       | `/photos/:id`      | `photos.show`   | `photos.process` |
| `GET`       | `/photos/:id/edit` | `photos.edit`   | `photos.edit`    |
| `PUT/PATCH` | `/photos/:id`      | `photos.update` | `photos.process` |
| `DELETE`    | `/photos/:id`      | `photos.delete` | `photos.process` |

## Baby got `back()!`

The framework super type now sports a `back()` function so you can use it to redirect back to the referer in a request.

```javascript
function save( event, rc, prc ){
    ... save your work
    
    // Go back to where you came from
    back();
}
```

`Here is the method signature:`

```javascript
/**
 * Redirect back to the previous URL via the referrer header, else use the fallback
 *
 * @fallback      The fallback event or uri if the referrer is empty, defaults to `/`
 * @persist       What request collection keys to persist in flash ram
 * @persistStruct A structure key-value pairs to persist in flash ram
 */
function back( fallback = "/", persist, struct persistStruct )
```

## `RequestContext` Routing/Pathing Enhancements

The RequestContext has a few new methods to assist you when working with routes, paths, and URLs.

| Method                                  | Purpose                                                                                     |
| --------------------------------------- | ------------------------------------------------------------------------------------------- |
| `routeIs( name ):boolean`               | Verify if the passed `name` is the current route                                            |
| `getUrl( withQuery:boolean )`           | Returns the entire URL, including the protocol, host, mapping, path info, and query string. |
| `getPath( withQuery:boolean )`          | Return the relative path of the current request.                                            |
| `getPathSegments():array`               | Get all of the URL path segments from the requested path.                                   |
| `getPathSegment( index, defaultValue )` | Get a single path segment by position                                                       |

## Native DateHelper

The `FrameworkSuperType` now has a `getDateTimeHelper(), getIsoTime()` methods to get access to the ColdBox `coldbox.system.async.time.DateTimeHelper` to assist you with all your date/time/timezone needs and generate an **iso8601** formatted string from the incoming date/time.

```javascript
getDateTimeHelper().toLocalDateTime( now(), "Americas/Central" )
getDateTimeHelper().getSystemTimezone()
getDateTimeHelper().parse( "2018-05-11T13:35:11Z" )
getIsoTime()
```

You can also use the new date time helper as a delegate in your models:

```javascript
component delegate="DateTime@coreDelegates"{

}
```

{% hint style="success" %}
Check out the API Docs for the latest methods in the helper

[https://s3.amazonaws.com/apidocs.ortussolutions.com/coldbox/7.0.0/coldbox/system/async/time/DateTimeHelper.html](https://s3.amazonaws.com/apidocs.ortussolutions.com/coldbox/7.0.0/coldbox/system/async/time/DateTimeHelper.html)
{% endhint %}

## View `variables` Scope Variables

You can now influence any view by injecting your own variables into a view's `variables` scope using our new argument: `viewVariables`.  This is great for module developers that want native objects or data to exist in a view's `varaibles` scope.

```javascript
view( view : "widget/messagebox", viewVariables : { wire : cbwire } 

view( view : "widget/client", viewVariables : { client : thisClient } )
```

Then you can use the `wire` and `client` objects in your views natively:

```html
<cfif client.isLoaded()>
    <h1>#client.getFullName()#</h1>
</cfif>
```

## Whoops! Upgrades

Whoops got even more love:

* SQL Syntax Highlighting
* JSON Pretty Printing
* JSON highlighting
* Debug Mode to show source code
* Production detection to avoid showing source code
* Rendering performance improvements

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption><p>SQL Hightlighting</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption><p>JSON Pretty Print + Highlights</p></figcaption></figure>



## RESTFul Exception Responses

The RESTFul handlers have been updated to present more useful debugging when exceptions are detected in any call to any resource.  You will now get the following new items in the response:

* `environment`
  * A snapshot of the current routes, URLs and event
* `exception`
  * A stack frame, detail, and type

```json
{
  "data": {
    "environment": {
      "currentRoutedUrl": "restfulHandler/anError/",
      "timestamp": "2023-04-26T20:21:05Z",
      "currentRoute": ":handler/:action/",
      "currentEvent": "restfulHandler.anError"
    },
    "exception": {
      "stack": [
        "/Users/lmajano/Sites/projects/coldbox-platform/system/web/context/RequestContext.cfc:1625",
        "/Users/lmajano/Sites/projects/coldbox-platform/test-harness/handlers/restfulHandler.cfc:42",
        "/Users/lmajano/Sites/projects/coldbox-platform/system/RestHandler.cfc:58",
        "/Users/lmajano/Sites/projects/coldbox-platform/system/web/Controller.cfc:998",
        "/Users/lmajano/Sites/projects/coldbox-platform/system/web/Controller.cfc:713",
        "/Users/lmajano/Sites/projects/coldbox-platform/test-harness/models/ControllerDecorator.cfc:71",
        "/Users/lmajano/Sites/projects/coldbox-platform/system/Bootstrap.cfc:290",
        "/Users/lmajano/Sites/projects/coldbox-platform/system/Bootstrap.cfc:506",
        "/Users/lmajano/Sites/projects/coldbox-platform/test-harness/Application.cfc:90"
      ],
      "detail": "The type you sent dddd is not a valid rendering type. Valid types are JSON,JSONP,JSONT,XML,WDDX,TEXT,PLAIN,PDF",
      "type": "RequestContext.InvalidRenderTypeException",
      "extendedInfo": ""
    }
  },
  "error": true,
  "pagination": {
    "totalPages": 1,
    "maxRows": 0,
    "offset": 0,
    "page": 1,
    "totalRecords": 0
  },
  "messages": [
    "An exception ocurred: Invalid rendering type"
  ]
}
```

## ColdBox `DebugMode`

ColdBox now has a global `debugMode` setting, which is used internally for presenting sources in Whoops and extra debugging parameters in our RESTFul handler.  However, it can also be used by module developers or developers to dictate if your application is in debug mode or not.  This is just a fancy way to say we trust the user doing the requests.

{% hint style="info" %}
&#x20;The default value is **false**
{% endhint %}

```
coldbox : {
    ...
    
    debugMode : true
    
    ...

}
```

You also have a `inDebugMode()` method in the main ColdBox Controller and the `FrameworkSuperType that will let you know if you are in that mode or not.`

```javascript
boolean function inDebugMode()

// Debug mode report
if( inDebugMode() ){
    include "BugReport.cfm";
}
```

You can also use the `AppModes@cbDelegates` delegate to get access to the `inDebugMode` and other methods in your **models**:

```javascript
component delegates="AppModes@cbDelegates"{

    ...
    if( inDebugMode() ){
    
    }
    ...

}
```

## Testing Enhancements

### Unload ColdBox is now false

All integration tests now won't unload ColdBox after execution.  Basically, the `this.unloadColdBox = false` is now the default.  This is to increase performance where each request run tries only to load the ColdBox virtual app once.

### Environment Detection Method

All test bundles now gets a `getEnv()` method to retrieve our environment delegate so you can get env settings and properties:

```javascript
getEnv().getSystemSetting()
getEnv().getSystemProperty()
getEnv().getEnv()
```

## Logging Enhancements

### JSON Pretty Output

All logging messages in LogBox are now inspected for simplicity or complexity.  If any extra argument is a complex variable, then LogBox will now serialize the output to JSON, and it will prettify it.  This will allow for your log messages in your console to now look pretty!

```json
[INFO ] 2023-04-26 15:48:47 coldbox.system.EventHandler Executing index action | ExtraInfo: {
	"ARCS":[
		1,
		2,
		3,
		4
	],
	"TEST":"Message goes here",
	"NAME":"Test",
	"WHEN":"April,
	26 2023 15:48:47 +0000"
}
```

### Exception Improvements

If exception objects are being sent to any logger, LogBox will detect it and pretty print it back to the console instead of dumping a massive exception object.

```log
[ERROR] 2023-04-26 16:10:25 coldbox.system.EventHandler Raw Exception ==> component [cbtestharness.models.myRequestContextDecorator] has no  function with name [getValuesss] | ExtraInfo:
ErrorType = expression
Message = component [cbtestharness.models.myRequestContextDecorator] has no  function with name [getValuesss]
StackTrace = lucee.runtime.exp.ExpressionException: component [cbtestharness.models.myRequestContextDecorator] has no  function with name [getValuesss]
TagContext =  ID: ??; LINE: 23; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/test-harness/handlers/testerror.cfc
 ID: ??; LINE: 1257; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/system/web/Controller.cfc
 ID: ??; LINE: 1006; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/system/web/Controller.cfc
 ID: ??; LINE: 713; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/system/web/Controller.cfc
 ID: ??; LINE: 71; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/test-harness/models/ControllerDecorator.cfc
 ID: ??; LINE: 290; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/system/Bootstrap.cfc
 ID: ??; LINE: 506; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/system/Bootstrap.cfc
 ID: ??; LINE: 90; TEMPLATE: /Users/lmajano/Sites/projects/coldbox-platform/test-harness/Application.cfc

ExtraInfo = ""
```

### Closure Logging

In previous versions, you had to use the `canxxxx` method to determine if you can log at a certain level:

```javascript
if( log.canInfo() ){
    log.info( "This is a log message", data );
}

if( log.canDebug() ){
    log.debug( "This is a debug message", data );
}
```

In ColdBox 7, you can use a one-liner by leveraging a lambda function:

```javascript
log.info( () => "This is a log message", data )
log.debug( () => "This is a debug message", data )
```

### Integration Testing Request Timeout

The RequestContext now has a `setRequestTimeout()` function that can provide timeouts in your application, and when in testing mode, it will mock the request timeout.  This is essential to encapsulate this setting as if you have requested timeout settings in your app; they will override the ones in testing.

```javascript
event.setRequestTimeout( 5 ) // 5 seconds
```

## Release Notes

You can find the [release notes](whats-new-with-7.0.0.md#release-notes) on the page below:

{% content-ref url="whats-new-with-7.0.0/release-notes.md" %}
[release-notes.md](whats-new-with-7.0.0/release-notes.md)
{% endcontent-ref %}

