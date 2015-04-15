# ColdBox Proxy

### Introduction

The ColdBox proxy enables remote applications or technologies like Flex, AIR, SOAP WebServices, RESTful Webservices and AJAX to communicate with ColdBox and provide an event driven model framework for those applications or for it to act as an enhanced service layer. Not only that, but you can reinitialize the entire application, get settings, announce custom or core interceptions, execute events, and so much more. You can create custom interceptor chains for your model that can be executed asynchronously when a user hits a save record button for example. You can create a Service Layer with built-in environmental settings, logging, error handling, event interception and chaining, you name it, and the possibilities are endless.

> The one key feature here, is that ColdBox morphs into a remote event-driven framework and no longer an MVC framework that produces HTML. 

![](ColdBoxProxy.jpg)

We not only give you the ability to do remote calls but also to monitor them. ColdBox has an execution monitor that can help you debug and analyze remote calls if you are in debug mode. This allows you to know what happens on asynchrounous calls from Flex/Air/SOAP, etc. The remote functionality of the framework enables you to actually create any amount of front ends using the same reusable ColdBox and model code. The code is the same, you create event handlers, you interact with a request collection, with core and custom plugins, but you don't set views or layouts because the framework is now a remote framework for your model. So what do you do, well, return data, arrays, xml, value objects. Anything, right from within the event handlers or setup a configuration setting that tells the framework to always return the request collection. You can also just create remote proxies to your service components (if using a service layers approach), so you can go directly to any object factory to request for services, interact with them and return results. The ColdBox proxy gives you flexibility in all aspects.

### Getting Started

Most remote APIs are strongly typed so it makes sense to create as many ColdBox proxy objects as you see fit. Don't just create one proxy with 1000 methods on it. Try to apply identity to these objects as well. We also recommend you create a remote folder in your application where you can store all your remote proxy objects.

```js
/Application
  /remote
    + MyProxy.cfc
```

The concept behind the ColdBox proxy is to create CFC's that extend our proxy class: coldbox.system.remote.ColdboxProxy. This will give you the ability to locate and talk to your running ColdBox application. However, since some of these requests won't be done via HTTP but other protocols like Flex/Air, your ColdBox application must know where in your server the application is located in. By default, when using HTTP calls, ColdBox can auto-locate your application with no issues at all, but with Flex/AIR or other protocols you must set this location in your Application.cfc.

##### AppMapping
In your Application.cfc you will find a directive called: COLDBOX_APP_MAPPING:

```js
<cfset COLDBOX_APP_MAPPING   = "">
```
This tells the framework where in the web server (sub-folder) your application is located in. By default, your application is the root of your website so this value is empty or / and you won't modify this. But if your application is in a sub-folder then add the full instantation path here. So if your application is under */apps/myApp* then the value would be:

```js
<cfset COLDBOX_APP_MAPPING   = "apps.myApp">
```

> **Infor** The AppMapping value is an instantiation path value

##### Proxy Example

The concept of a [Proxy](http://en.wikipedia.org/wiki/Proxy_pattern) is to give access to another system. As Wikipedia mentions:

> "A proxy, in its most general form, is a class functioning as an interface to something else. The proxy could interface to anything: a network connection, a large object in memory, a file, or some other resource that is expensive or impossible to duplicate" <small> Wikipedia </small>

The proxy will give you access to your entire ColdBox application assets but also allow you to proxy in request to the normal ColdBox event model. You will do this via our `process()` method. This method expects a event argument to be passed to it which is the event that will be executed for you and all the other arguments passed to this method will be converted and merged into the RequestContext's request collection. Then your event handlers can respond to these requests just like normal requests and even return data back to the caller. The advanced ColdBox template gives you a sample proxy object in your *remote/MyProxy.cfc* folder.