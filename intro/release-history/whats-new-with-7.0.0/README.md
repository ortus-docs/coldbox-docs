---
description: Discover the power of ColdBox 7.0.0
---

# What's New With 7.0.0

ColdBox 7.0.0 is a major release for the ColdBox HMVC platform. It has some dramatic new features as we keep pushing for more modern and sustainable approaches to web development and tons of bug fixes and improvements.

We break down the major areas of development below, and you can also find the [full release notes](release-notes.md) per library at the end.

## Engine Support

<figure><img src="../../../.gitbook/assets/coldbox-engine-support.jpeg" alt=""><figcaption><p>ColdBox v7 Engine Support</p></figcaption></figure>

This release drops support for Adobe 2016 and adds support for Adobe 2023 and Lucee 6 (Beta).  Please note that there are still issues with Adobe 2023 as it is still in Beta.

## User Identifier Providers

In previous versions of ColdBox, it would auto-detect unique request identifiers for usage in Flash Ram, storages, etc following this schema:

1. If we have `session` enabled, use the `jessionId` or `session` URL Token
2. If we have cookies enabled, use the `cfid/cftoken`
3. If we have in the `URL` the `cfid/cftoken`
4. Create a unique request-based tracking identifier: `cbUserTrackingId`

However, you can now decide what will be the unique identifier for requests by providing it via a `coldbox.identifierProvider` as a closure/lambda in your `config/Coldbox.cfc`

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

## Module Injectors



## Delegates







## RESTFul Exception Responses

The RESTFul handlers have been updated to present more useful debugging when exceptions are detected in any call to any resource.  You will now get the following new items in the response:

* `environment`
  * A snapshot of the current routes, urls and event
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

### Env Detection

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



## Release Notes

You can find the [release notes](./#release-notes) on the page below:

{% content-ref url="release-notes.md" %}
[release-notes.md](release-notes.md)
{% endcontent-ref %}

