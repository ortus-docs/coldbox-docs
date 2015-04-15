# Distinguishing Request Types

Now, what if you want to distinguish between a normal request and a proxy request? Well, the request context object, most commonly known as the event object has a method called:

* isProxyRequest : This boolean method determines what type of request is being executed.

This is extremely useful if you are doing path operations. Why? Well, when you call a coldfusion component via flash remoting or live data cycle services, the expandPath, getCurrentTemplatePath, getBaseTemplatePath, and other template path methods will always give you the path according to the file you are currently executing. This is due to how ColdFusion deals with remote calls. So you can use the method below to distinguish and load accordingly:

```js
<---  Use the isProxyRequest() --->
<cfif event.isProxyRequest()>
	<cfset getPlugin("JavaLoader").setup( listToArray( ExpandPath("../includes/helloworld.jar")) )>
<cfelse>
	<cfset getPlugin("JavaLoader").setup( listToArray( ExpandPath("includes/helloworld.jar")) )>
</cfif>
```

