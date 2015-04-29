# Getting Started

Most remote APIs are strongly typed so it makes sense to create as many ColdBox proxy objects as you see fit. Don't just create one proxy with 1000 methods on it. Try to apply identity to these objects as well. We also recommend you create a `remote` folder in your application where you can store all your remote proxy objects.

```js
/Application
  /remote
    + MyProxy.cfc
```

The concept behind the ColdBox proxy is to create CFC's that extend our proxy class: `coldbox.system.remote.ColdboxProxy`. This will give you the ability to locate and talk to your running ColdBox application. 

```js
component extends="coldbox.system.remote.ColdboxProxy"{

}
```

## AppMapping

However, since some of these requests won't be done via HTTP but other protocols like Flex/Air, your ColdBox application must know where in your server the application is located in. By default, when using HTTP calls, ColdBox can auto-locate your application with no issues at all, but with Flex/AIR or other protocols you must set this location in your `Application.cfc` via the `COLDBOX_APP_MAPPING` directive.

```js
<cfset COLDBOX_APP_MAPPING   = "">
```

This tells the framework where in the web server (sub-folder) your application is located in. By default, your application is the root of your website so this value is empty or `/` and you won't modify this. But if your application is in a sub-folder then add the full instantation path here. So if your application is under `/apps/myApp` then the value would be:

```js
<cfset COLDBOX_APP_MAPPING   = "apps.myApp">
```

> **Info** The `COLDBOX_APP_MAPPING` value is an instantiation path value
