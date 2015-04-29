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

The concept of a [Proxy](http://en.wikipedia.org/wiki/Proxy_pattern) is to give access to another system. As Wikipedia mentions:

> "A proxy, in its most general form, is a class functioning as an interface to something else. The proxy could interface to anything: a network connection, a large object in memory, a file, or some other resource that is expensive or impossible to duplicate" <small> Wikipedia </small>

The proxy will give you access to your entire ColdBox application assets but also allow you to proxy in request to the normal ColdBox event model. You will do this via our `process()` method. This method expects an `event` argument to be passed to it which is the event string that will be executed for you and all the other arguments passed to this method will be converted and merged into the request collection. Then your event handlers can respond to these requests just like normal requests and even return data back to the caller. The advanced ColdBox template gives you a sample proxy object in your `remote/MyProxy.cfc` folder.

```js
<cfcomponent name="MyProxy" output="false" extends="coldbox.system.remote.ColdboxProxy">

	<cffunction name="yourRemoteCall" output="false" access="remote" returntype="YourType" hint="Your Hint">
		<cfset var results = "">

		<---  Set the event to execute --->
		<cfset arguments.event = "">

		<---  Call to process a coldbox event cycle, always check the results as they might not exist. --->
		<cfset results = super.process(argumentCollection=arguments)>

		<cfreturn results>
	</cffunction>

</cfcomponent>
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
