# Proxy Example


##### Proxy Example

The concept of a [Proxy](http://en.wikipedia.org/wiki/Proxy_pattern) is to give access to another system. As Wikipedia mentions:

> "A proxy, in its most general form, is a class functioning as an interface to something else. The proxy could interface to anything: a network connection, a large object in memory, a file, or some other resource that is expensive or impossible to duplicate" <small> Wikipedia </small>

The proxy will give you access to your entire ColdBox application assets but also allow you to proxy in request to the normal ColdBox event model. You will do this via our `process()` method. This method expects a event argument to be passed to it which is the event that will be executed for you and all the other arguments passed to this method will be converted and merged into the [RequestContext](http://wiki.coldbox.org/wiki/RequestContext.cfm)'s request collection. Then your event handlers can respond to these requests just like normal requests and even return data back to the caller. The advanced ColdBox template gives you a sample proxy object in your *remote/MyProxy.cfc* folder.

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

This simple proxy object extends the ColdBox Proxy class that gives you all the remote abilities. Then it is up to you to create methods that will respond to either Flex/Air/Soap or now with ColdFusion 10; Restful services.
