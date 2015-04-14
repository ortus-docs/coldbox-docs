# Techniques

There are two techniques when interfacing with the proxy:

1. Calling the `process()` method or calling a method that uses the process() method to execute an event within your application.
2. Calling a method in a proxy object that get's data from a service layer directly via the object retrieval methods.

Which one you use, well depends on your requirements and interactivity. If you have a service layer that can provide you with data directly with no request flow or security, then use method 2, else maybe method 1 is your cup of tea.

### How?

The greatest question of all. Well, in the distro application template you will find a basic coldboxproxy.cfc that can be used for remote operations. The inherent basics here is that you have a component that extends the base coldbox proxy class: extends=coldbox.system.extras.ColdBoxProxy. Below is this simple cfc

```js
<cfcomponent name="coldboxproxy" output="false" extends="coldbox.system.extras.ColdboxProxy">

	<---  You can override this method if you want to intercept before and after. --->
	<cffunction name="process" output="false" access="remote" returntype="any" hint="Process a remote call and return data/objects back.">
		<cfset var results = "">
		
		<---  Anything before --->
		
		<---  Call the actual proxy --->
		<cfset results = super.process(argumentCollection=arguments)>
		
		<---  Anything after --->
		
		<cfreturn results>
	</cffunction>
</cfcomponent>
```

You can use this simple proxy to call events via the process method. How? Here is a javascript sample using cfajaxproxy:

```js
<---  Declare the CF Ajax Proxy HERE --->
<cfajaxproxy cfc="coldboxproxy.cfc" jsclassname="cbProxy">

<script type="text/javascript">
var getArtists = function(){
	var cbox = new cbProxy();
      // Setting a callback handler for the proxy automatically makes
      // the proxy's calls asynchronous.
      cbox.setCallbackHandler(populateArtists);
      cbox.setErrorHandler(myErrorHandler);
  	  // The proxy getArtists function represents the CFC
  	  // getArtists function.
      cbox.process(event:'artists.list');
 }
</script>
```