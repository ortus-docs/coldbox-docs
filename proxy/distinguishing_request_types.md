# Distinguishing Request Types

Now, what if you want to distinguish between a normal request and a proxy request? Well, the request context object, most commonly known as the event object has a method called:

* `isProxyRequest` : This boolean method determines what type of request is being executed.

This is extremely useful if you are doing path operations. Why? Well, when you call a coldfusion component via flash remoting or live data cycle services, the expandPath, getCurrentTemplatePath, getBaseTemplatePath, and other template path methods will always give you the path according to the file you are currently executing. This is due to how ColdFusion deals with remote calls. So you can use the method below to distinguish and load accordingly:

```js
<---  Use the isProxyRequest() --->
<cfif event.isProxyRequest()>
	<cfset getPlugin("JavaLoader").setup( listToArray( ExpandPath("../includes/helloworld.jar")) )>
<cfelse>
	<cfset getPlugin("JavaLoader").setup( listToArray( ExpandPath("includes/helloworld.jar")) )>
</cfif>
```

The above code will be executed within the proxy as if it’s in the handler’s directory. So to expand the jar file path in the includes directory, you have to go back a level when using the proxy and just directly if not. This is very important to grasp, since its not !ColdBox doing this, but ColdFusion. Executions via http protocols might not exhibit the same behavior.



