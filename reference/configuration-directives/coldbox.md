---
description: Every core ColdBox configuration directive — application setup, conventions, environments, flash, interceptors, layouts, and custom settings.
icon: gear
---

# ColdBox Directives

The `coldbox` directive is where you configure the framework for operation. This page consolidates all core configuration sections: application setup, conventions, environments, flash, interceptors, layouts, and custom settings.

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
**Removed:** `appMapping` is **not** a `coldbox = {}` setting. The application mapping is configured via the `COLDBOX_APP_MAPPING` variable in your `Application.bx`/`Application.cfc`. See [Application.bx (or .cfc for CFML)](../../getting-started/configuration/bootstrapper-application.cfc.md) for details.
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

Will scan the conventions directory for new handler classes on each request if activated. Use **false** for production, this is only a development true setting.

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

These settings map 1-1 from ColdBox events to the `Application.bx` (or `.cfc` for CFML) life-cycle methods. The only one that is not is the `defaultEvent`, which selects what event the framework will execute when no incoming event is detected via URL/FORM or REMOTE executions.

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

The CF dot notation path of the class that will decorate the system Request Context object.

### **controllerDecorator**

The CF dot notation path of the class that will decorate the system Controller

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
    identifierProvider = () => {
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

Unlike the settings above, `async` and `sse` are **not** nested inside the `coldbox = {}` struct - they are their own top-level structures in your `ColdBox.bx` (or `.cfc` for CFML), right alongside `coldbox`, `conventions`, `interceptors`, etc.

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

The number of threads to allocate to the executor backing the global application [Scheduler](../../digging-deeper/scheduled-tasks.md) (`appScheduler@coldbox`). Defaults to **20**.

### **sse.keepAliveInterval**

Milliseconds between automatic keep-alive comments sent down an open [Server-Sent Events](../../the-basics/event-handlers/server-sent-events.md) stream so intermediary proxies don't idle it out. Defaults to **30000** (30 seconds). Set to `0` to disable. Can be overridden per-call on `event.sse()`.

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

The name of the currently active [environment](#environments). Defaults to `production` and is normally set for you by the environment detection process (regex matching, the `ENVIRONMENT` system/environment variable, or a custom `detectEnvironment()`), but you can also read/override it as a normal setting via `getSetting( "environment" )`.

### **debugMode**

Activates the framework's debug mode, which enables more verbose/robust exception reporting (e.g. the Whoops error handler) and other development-time conveniences. Defaults to **false**. Check it at runtime with `controller.inDebugMode()`. **Always leave this off in production.**

### **exceptionEditor**

The identifier of the IDE/editor used to build clickable "open file" links for stack trace entries in exception reports (e.g. `BugReport.bxm` (or `.cfm` for CFML)). Defaults to `vscode`.

---

# Conventions

This element defines custom conventions for your application. By default, the framework has a default set of conventions that you need to adhere too. However, if you would like to implement your own conventions for a specific application, you can use this setting, otherwise do not declare it:

```javascript
//Conventions
conventions = {
    handlersLocation = "controllers",
    viewsLocation      = "views",
    layoutsLocation  = "views",
    modelsLocation      = "model",
    modulesLocation  = "modules",
    includesLocation = "includes",
    eventAction      = "index"
};
```

## Resulting Framework Settings

Behind the scenes, each key you override above (if any) is translated into one of the following framework settings, which is what the framework and its services actually read from at runtime. You can also read any of them directly via `getSetting( name )`:

| `conventions` key  | Framework Setting   | Default        |
| ------------------ | -------------------- | -------------- |
| `handlersLocation`  | `handlersConvention` | `handlers`     |
| `viewsLocation`     | `viewsConvention`     | `views`        |
| `layoutsLocation`   | `layoutsConvention`   | `layouts`      |
| `modelsLocation`    | `modelsConvention`    | `models`       |
| `modulesLocation`   | `modulesConvention`   | `modules`      |
| `includesLocation`  | `includesConvention`  | `includes`     |
| `eventAction`       | `eventAction`         | `index`        |

{% hint style="info" %}
`configConvention` (default `config.Coldbox`, the dot-notation invocation path used to locate your `config/ColdBox.bx` (or `.cfc` for CFML)) is also a framework setting, but it is resolved before conventions are parsed and cannot be overridden via the `conventions` struct.
{% endhint %}

---

# Environments

The configuration class has embedded environment control and detection built-in. Environments can be detected by:

* regex matching against cgi.http\_host
* detection of an environmental variable called ENVIRONMENT ( Coldbox 5.2 and higher )
* usage of a `detectEnvironment()` function

The first option (regex matching) is the easiest to use, but not very reliable if you are using multiple hostnames or commandbox for re-initialization.

{% hint style="warning" %}
If you are using `commandbox` please read ALL options below
{% endhint %}

## Default: Regex matching against cgi.http\_host

To detect your environments you will setup a structure called `environments` in your coldbox configuration with the named environments and their associated regular expressions for its `cgi` host names to match for you automatically. If the framework matches the regex with the associated `cgi.http_host`, it will set a setting called `Environment` in your configuration settings and look for that environment setting name in your class as a method by convention. That's right, it will check if your class has a method with the same name as the environment and if it exists, it will call it for you. Here is where you basically override, remove, or add any settings according to your environment.

> **Warning** : The environment detection occurs AFTER the `configure()` method is called. Therefore, whatever settings or configurations you have on the `configure()` method will be stored first, treat those as **Production** settings.

```javascript
environments = {
    // The key is the name of the environment
    // The value is a list of regex to match against cgi.http_host
    development = "^cf2016.,^lucee.,localhost",
    staging = "^stg"
};
```

The regex match will also create a global setting called "environment" which you can access and use like this:

```javascript
if ( getSetting('environment') == 'development' ){
    doSomeMajik();
}
```

In the above example, I declare a **development** key with a value list of regular expressions. If I am in a host that starts with **cf2016**, this will match and set the environment setting equal to **development**. It will then look for a development method in this class and execute it.

```javascript
/**
* Executed whenever the development environment is detected
*/
function development(){
    // Override coldbox directives
    coldbox.handlerCaching = false;
    coldbox.eventCaching = false;
    coldbox.debugPassword = "";
    coldbox.reinitPassword = "";

    // Add dev only interceptors
    arrayAppend( interceptors, {class="#appMapping#.interceptors.CustomLogger} );
}
```

## Detection of an environmental variable called ENVIRONMENT

If you are using environmental variables for your different environments, you can specify an environmental variable called ENVIRONMENT and name it `staging`, `development`, `testing` etcetera, depending on the required environment. As in the regex example, a function named after your environment (e.g. `staging()` or `development()` ) will be called after your `configure` method.

{% hint style="info" %}
This method is more reliable than relying on cgi.http\_host, since it will never change once configured correctly.
{% endhint %}

## Custom Environment Detection

If you are NOT using environmental variables you can use your own detection algorithm instead of looking at the `cgi.http_host` variable. You will NOT fill out an environments structure but actually create a method with the following signature:

```javascript
string public function detectEnvironment(){
}
```

This method will be executed for you at startup and it must return the name of the environment the application is on. You can check for any condition which distinguishes your environment from your other environments. As long as you return an environment name based on your own logic it will then store it and execute the method if it exists.

---

# Flash

This directive is how you will configure the [Flash RAM](../../digging-deeper/flash-ram/) for operation. Below are the configuration keys and their defaults:

```javascript
// flash scope configuration
flash = {
    // Available scopes are: session,client,cache,mock or your own class path
    scope = "session",
    // constructor properties for the flash scope implementation
    properties = {},
    // automatically inflate flash data into the RC scope at the beginning of a request
    inflateToRC = true, 
    // automatically inflate flash data into the PRC scope at the beginning of a request
    inflateToPRC = false, 
    // automatically purge flash data for you
    autoPurge = true, 
    // automatically save flash scopes at end of a request and on relocations.
    autoSave = true 
};
```

---

# Interceptors

This is an array of interceptor definitions that you will use to register in your application. The key about this array is that **ORDER** matters. The interceptors will fire in the order that you register them whenever their interception points are announced, so please watch out for this caveat. Each array element is a structure that describes what interceptor to register.

```javascript
//Register interceptors as an array, we need order
interceptors = [

    { 
        // The class instantiation path
        class="",
        // The alias to register in WireBox, if not defined it uses the name of the class
        name="",
        // A struct of data to configure the interceptor with.
        properties={}
    }

    { class="mypath.MyInterceptor",
      name="MyInterceptor",
      properties={useSetterInjection=false}
    }
];
```

> **Warning** : Important: Order of declaration matters! Also, when declaring multiple instances of the same class (interceptor), make sure you use the name attribute in order to distinguish them. If not, only one will be registered (the last one declared).

---

# InterceptorSettings

This structure configures the interceptor service in your application.

```javascript
//Interceptor Settings
interceptorSettings = {
    throwOnInvalidStates = false,
    customInterceptionPoints = "onLogin,onWikiTranslation,onAppClose"
};
```

## `throwOnInvalidStates`

This tells the interceptor service to throw an exception if the state announced for interception is not valid or does not exist. Defaults to **false**.

## `customInterceptionPoints`

This key is a comma delimited list or an array of custom interception points you will be registering for custom announcements in your application. This is the way to provide an observer-observable pattern to your applications.

> **Info** Please see the [Interceptors](../../the-basics/interceptors/) section for more information.

---

# Layouts

The layouts array element is used to define implicit associations between layouts and views/folders, this does not mean that you need to register ALL your layouts. This is a convenience for pairing them, we are in a conventions framework remember.

Before any renderings occur or lookups, the framework will check this array of associations to try and match in what layout a view should be rendered in. It is also used to create aliases for layouts so you can use aliases in your code instead of the real file name and locations.

```javascript
//Register Layouts
layouts = [
    { 
        // The alias of a layout
        name="",
        // The layout file
        file="",
        // A list of view names to render within this layout
        views="",
        // A list of regex names to match a view
        folders=""
    }

    // Examples
    { name="tester",file="Layout.tester.cfm",views="vwLogin,test",folders="tags,pdf/single"    },
    { name="login",file="Login.cfm",folders="^admin/security"}
];
```

---

# LayoutSettings

This structure allows you to define a system-wide default layout and view.

```javascript
//Layout Settings
layoutSettings = {
    // The default layout to use if NOT defined in a request
    defaultLayout = "Main",
    // The default view to use to render if NOT defined in a request
    defaultView   = "youForgot"
};
```

> **Hint** Please remember that the default layout is `Main.cfm`

---

# Settings

These are custom application settings that you can leverage in your application.

```javascript
// Custom Settings
settings = {
    useSkins = true,
    myCoolArray = [1,2,3,4],
    skinsPath = "views/skins",
    myUtil = createObject("component","#appmapping#.model.util.MyUtility")
};
```

You can read our [Using Settings](../../getting-started/configuration/using-settings.md) section to discover how to use all the settings in your application.
