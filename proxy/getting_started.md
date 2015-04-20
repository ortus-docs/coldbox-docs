# Getting Started

### Getting Started

Most remote APIs are strongly typed so it makes sense to create as many ColdBox proxy objects as you see fit. Don't just create one proxy with 1000 methods on it. Try to apply identity to these objects as well. We also recommend you create a remote folder in your application where you can store all your remote proxy objects.

```js
/Application
  /remote
    + MyProxy.cfc
```

The concept behind the ColdBox proxy is to create CFC's that extend our proxy class: coldbox.system.remote.ColdboxProxy. This will give you the ability to locate and talk to your running ColdBox application. However, since some of these requests won't be done via HTTP but other protocols like Flex/Air, your ColdBox application must know where in your server the application is located in. By default, when using HTTP calls, ColdBox can auto-locate your application with no issues at all, but with Flex/AIR or other protocols you must set this location in your Application.cfc.