# ColdBox

The ColdBox directive is where you configure the framework for operation.

## Application Setup

```javascript
coldbox = {
    // The name of the application
    appName     = "My App",
    // The name of the incoming URL/FORM/REMOTE variable that tells the framework what event to execute. Ex: index.cfm?event=users.list
    eventName   = "event"
};
```

> **Info** : Please note that there are no mandatory settings as of ColdBox 4.2.0. If fact, you can remove the config file completely and your app will run. It will be impossible to reinit the app however without a reinit password set.

{% hint style="danger" %}
**Removed:** `appMapping` is **not** a `coldbox = {}` setting. The application mapping is configured via the `COLDBOX_APP_MAPPING` variable in your `Application.cfc`/`Application.bx`. See [Application.cfc](../../bootstrapper-application.cfc.md) for details.
{% endhint %}

## Development Settings

```javascript
coldbox = {
    reinitPassword = "h1cker",
    reinitKey = "fwReinit",
    handlersIndexAutoReload = true
};
```

### **reinitPassword**

Protect the reinitialization of the framework URL actions. For security, if this setting is omitted, we will create a random password. Setting it to an empty string will allow you to reinitialize without a password. **Always have a password set for public-facing sites.**

```
// reinit with no password
http://localhost/?fwreinit=1
// reinit with password
http://localhost/?fwreinit=mypass
```

### **reinitKey**

The key used in FORM or URL to reinit the framework. The default is `fwreinit` but you can change it to whatever you like.

```javascript
coldbox = {
    reinitKey = "myreinit"
}
```

### **handlersIndexAutoReload**

Will scan the conventions directory for new handler CFCs on each request if activated. Use **false** for production, this is only a development true setting.

## Implicit Event Settings

```javascript
coldbox={
    //Implicit Events
    defaultEvent  = "Main.index",
    requestStartHandler     = "Main.onRequestStart",
    requestEndHandler   = "Main.onRequestEnd",
    applicationStartHandler = "Main.onAppInit",
    applicationEndHandler = "Main.onAppEnd",
    sessionStartHandler = "Main.onSessionStart",
    sessionEndHandler = "Main.onSessionEnd",
    missingTemplateHandler = "Main.onMissingTemplate"
}
```

These settings map 1-1 from ColdBox events to the `Application.cfc` life-cycle methods. The only one that is not is the `defaultEvent`, which selects what event the framework will execute when no incoming event is detected via URL/FORM or REMOTE executions.

## Extension Points

The ColdBox extension points are a great way to create federated applications that can reuse a centralized core instead of the local conventions. It is also a great way to extend some core classes with your own.

```javascript
coldbox={
    //Extension Points
    applicationHelper             = "includes/helpers/ApplicationHelper.cfm",
    viewsHelper                    = "",
    modulesExternalLocation        = [],
    viewsExternalLocation        = "",
    layoutsExternalLocation     = "",
    handlersExternalLocation      = "",
    requestContextDecorator     = "",
    controllerDecorator         = ""
}
```

### **applicationHelper**

A list or array of absolute or relative paths to a UDF helper file. The framework will load all the methods found in this helper file globally. Meaning it will be injected in ALL handlers, layouts and views.

### **viewsHelper**

This is a location within your application or an absolute path to a `cfm or bxm` template that will act as your global helper for all rendered views.

```json
coldbox.viewsHelper = "includes/helpers/global"
```

### **modulesExternalLocation**

A list or array of locations of where ColdBox should look for modules to load into your application. The path can be a cf mapping or `cfinclude` compatible location. Modules are searched and loaded in the order of the declared locations. The first location ColdBox will search for modules is the conventions folder `modules`

### **viewsExternalLocation**

The CF include path of where to look for secondary views for your application. Secondary views look just like normal views except the framework looks in the conventions folder first and if not found then searches this location.

### **layoutsExternalLocation**

The CF include path of where to look for secondary layouts for your application. Secondary layouts look just like normal layouts except the framework looks in the conventions folder first and if not found then searches this location.

### **handlersExternalLocation**

The CF dot notation path of where to look for secondary events for your application. Secondary events look just like normal events except the framework looks in the conventions folder first and if not found then searches this location.

### **requestContextDecorator**

The CF dot notation path of the CFC that will decorate the system Request Context object.

### **controllerDecorator**

The CF dot notation path of the CFC that will decorate the system Controller

## Exception Handling

```javascript
coldbox = {
    // Error/Exception Handling handler
    exceptionHandler        = "",
    // Invalid HTTP method Handler
    invalidHTTPMethodHandler = "",
    // The handler to execute on invalid events
    invalidEventHandler = "",
    // The default error template    
    customErrorTemplate     = "/coldbox/system/exceptions/BugReport-Public.cfm"
}
```

### **exceptionHandler**

The event handler to call whenever ANY non-catched exception occurs anywhere in the request lifecycle execution. Before this event is fired, the framework will log the error and place the exception in the prc as `prc.exception`.

### **invalidHTTPMethodHandler**

The event handler to call whenever a route or event is accessed with an invalid HTTP method.

### **invalidEventHandler**

This is the event handler that will fire masking a non-existent event that gets requested. This is a great place to place 302 or 404 redirects whenever non-existent events are being requested.

### **customErrorTemplate**

The relative path from the application's root level of where the custom error template exists. This template receives a key in the private request collection called `exception` that contains the exception. By default ColdBox does not show robust exceptions, you can turn on robust exceptions by choosing the following template:

```
coldbox.customErrorTemplate = "/coldbox/system/exceptions/BugReport.cfm";
```

## Application Aspects

```javascript
coldbox = {
    // Persist handlers
    handlerCaching             = false,
    // Activate event caching
    eventCaching            = true,
    // Activate view  caching
    viewCaching              = true,
    // Cache the discovery of views/layouts on disk (module/external locations, extensions, etc.)
    viewDiscoveryCaching     = true,
    // Merge an incoming JSON body payload into the RC collection
    jsonPayloadToRC           = true,
    // Return RC struct on Flex/Soap Calls
    proxyReturnCollection     = false,
    // Activate implicit views
    implicitViews           = true,
    // Case for implicit views
    caseSensitiveImplicitViews = true,
    // Auto register all model objects in the `models` folder into WireBox
    autoMapModels     = true,
    // Your very own session tracking identifier
    identifierProvider = function(){
        return my own session tracking id;
    }
}
```

### **autoMapModels**

ColdBox by convention can talk to, use and inject models from the `models` folder by just using their name. On startup it will scan your entire `models` folder and will register all the discovered models. This setting is **true** by default.

### **caseSensitiveImplicitViews**

By default implicit views are case sensitive since ColdBox version 5.2.0, before this version the default was **false**.

### **eventCaching**

This directive tells ColdBox that when events are executed they will be inspected for caching metadata. This does not mean that ALL events WILL be cached if this setting is turned on. It just activates the inspection mechanisms for whenever you annotate events for caching or using the `runEvent()` caching methods.

### **handlerCaching**

This is useful to be set to false in development and true in production. This tells the framework to cache your event handler objects as singletons.

### **implicitViews**

Allows you to use implicit views in your application and view dispatching. You can get a performance boost if you disable this setting.

### identifierProvider

This setting allows you to configure a lambda/closure that will return back the user's request identifier according to your own algorithms. This overrides the internal way ColdBox identifies requests incoming to the application which are used internally to track sessions, flash rams, etc.

The discovery algorithm we use is the following:

1. If we have an `identifierProvider` closure/lambda/udf, then call it and use the return value
2. If we have sessions enabled, use the `jessionId` or session URL Token
3. If we have cookies enabled, use the `cfid/cftoken`
4. If we have in the URL the `cfid/cftoken`
5. Create a request based tracking identifier: `cbUserTrackingId`

### **proxyReturnCollection**

This is a boolean setting used when calling the ColdBox proxy's `process()` method from a Flex or SOAP/REST call. If this setting is set to **true**, the proxy will return back to the remote call the entire request collection structure ALWAYS! If set to **false**, it will return, whatever the event handler returned back. Our best practice is to always have this **false** and return appropriate data back.

### **viewCaching**

This directive tells ColdBox that when views are rendered, the `cache=true` parameter will be obeyed. Turning on this setting will not cause any views to be cached unless you are also passing in the caching parameters to your `view()` or `event.setView()` calls.

### **viewDiscoveryCaching**

This directive is independent from `viewCaching` (which caches rendered **output**). It caches the **discovery** process the renderer uses to locate a view/layout on disk (module or external locations, file extension resolution, etc.), so it doesn't have to re-resolve the same view/layout path on every request. Defaults to **true**; you may want to disable it in development if you are actively moving views/layouts around.

### **jsonPayloadToRC**

When the incoming request body is a JSON payload, ColdBox will parse it and merge it into the `RC` (Request Collection) automatically, just like `FORM`/`URL` variables. Defaults to **true**. Set to **false** if you'd rather read the raw JSON body yourself via `event.getHTTPContent()`.

## Async & Server-Sent Events Settings

Unlike the settings above, `async` and `sse` are **not** nested inside the `coldbox = {}` struct - they are their own top-level structures in your `ColdBox.cfc`, right alongside `coldbox`, `conventions`, `interceptors`, etc.

```javascript
// Async Executor Settings
async = {
    // Number of threads for the global app scheduler's executor
    schedulerThreads = 20
};

// Server-Sent Events Settings (BoxLang only)
sse = {
    // Milliseconds between automatic keep-alive comments. Most proxies idle out at 60s.
    keepAliveInterval = 30000,
    // Client reconnect hint in milliseconds sent to the browser. 0 omits the field.
    retry             = 0,
    // CORS origin allowed to consume the stream
    cors              = "*"
};
```

### **async.schedulerThreads**

The number of threads to allocate to the executor backing the global application [Scheduler](../../../../digging-deeper/scheduled-tasks.md) (`appScheduler@coldbox`). Defaults to **20**.

### **sse.keepAliveInterval**

Milliseconds between automatic keep-alive comments sent down an open [Server-Sent Events](../../../../the-basics/event-handlers/server-sent-events.md) stream so intermediary proxies don't idle it out. Defaults to **30000** (30 seconds). Set to `0` to disable. Can be overridden per-call on `event.sse()`.

### **sse.retry**

The client reconnect hint, in milliseconds, sent to the browser's `EventSource` so it knows how long to wait before automatically reconnecting after a dropped stream. Defaults to `0`, which omits the field entirely. Can be overridden per-call on `event.sse()`.

### **sse.cors**

The `Access-Control-Allow-Origin` value written for SSE responses. Defaults to `*` (allow all origins). Can be overridden per-call on `event.sse()`.

## Environment & Debugging Settings

```javascript
coldbox = {
    // The name of the currently detected environment
    environment      = "production",
    // Turn on/off debug mode for the framework
    debugMode        = false,
    // The IDE/Editor used to open file links in exception reports
    exceptionEditor  = "vscode"
}
```

### **environment**

The name of the currently active [environment](environments.md). Defaults to `production` and is normally set for you by the environment detection process (regex matching, the `ENVIRONMENT` system/environment variable, or a custom `detectEnvironment()`), but you can also read/override it as a normal setting via `getSetting( "environment" )`.

### **debugMode**

Activates the framework's debug mode, which enables more verbose/robust exception reporting (e.g. the Whoops error handler) and other development-time conveniences. Defaults to **false**. Check it at runtime with `controller.inDebugMode()`. **Always leave this off in production.**

### **exceptionEditor**

The identifier of the IDE/editor used to build clickable "open file" links for stack trace entries in exception reports (e.g. `BugReport.cfm`). Defaults to `vscode`.
