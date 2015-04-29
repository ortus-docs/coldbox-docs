# Distinguishing Request Types

Now, what if you want to distinguish between a normal request and a proxy request? Well, the request context object, most commonly known as the event object has a method called:

* `isProxyRequest` : This boolean method determines what type of request is being executed.

```js
<---  Use the isProxyRequest() --->
<cfif event.isProxyRequest()>
	<cfset getPlugin("JavaLoader").setup( listToArray( ExpandPath("../includes/helloworld.jar")) )>
<cfelse>
	<cfset getPlugin("JavaLoader").setup( listToArray( ExpandPath("includes/helloworld.jar")) )>
</cfif>
```

The above code will be executed within the proxy as if it’s in the handler’s directory. So to expand the jar file path in the includes directory, you have to go back a level when using the proxy and just directly if not. This is very important to grasp, since its not !ColdBox doing this, but ColdFusion. Executions via http protocols might not exhibit the same behavior.



