# The Base Proxy Object

Here are some common methods of our ColdBox proxy object.  However, we encourage you to see the [API docs](http://apidocs.ortussolutions.com/coldbox/current) for that latest and greatest.

|Method|Description|
|--|--|
|announceInterception(state, data) |Processes a remote interception.|
|getCacheBox()	|Get a reference to [CacheBox](http://wiki.coldbox.org/wiki/CacheBox.cfm)|
|getCache(cacheName='default')	|Get a reference to a named cache provider|
|getController() |Returns the ColdBox controller instance |
|getInterceptor()	|Get a named interceptor |
|getLogBox()	|Get a reference to LogBox|
|getModel(name,dsl,initArguments)	|Get a [Wirebox](http://wiki.coldbox.org/wiki/Wirebox.cfm) model object|
|getWireBox()	|Get a reference to [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm)|
|process() |Processes a remote call that will execute a coldbox event and returns data/objects back. |
|loadColdBox()	|Gives you the ability to load any external coldbox application in the application scope. Great for remotely loading any coldbox application, it can be located anywhere.|
|getRemotingUtil()	| Return a utility class to manipulate output and buffer |

## Expanding Theory

Below is a custom method that I created in my own application proxy for retrieving application settings. I DO NOT recommend this, since it will expose your settings. This is just a sample

```js
<---  Get a setting --->
<cffunction name="getSetting" hint="I get a setting from the FW Config structures. Use the FWSetting boolean argument to retrieve from the fwSettingsStruct." access="remote" returntype="struct" output="false">
<---************************************************************** --->
<cfargument name="name" 	    type="string"   	hint="Name of the setting key to retrieve"  >
<cfargument name="FWSetting"  	type="boolean" 	 	required="false"  hint="Boolean Flag. If true, it will retrieve from the fwSettingsStruct else from the configStruct. Default is false." default="false">
<---************************************************************** --->
<cfscript>
var cbController = "";
var setting = "";

cbController = getController();


//Get Setting else return ""
if( cbController.settingExists(argumentCollection=arguments) ){
	setting = cbController.getSetting(argumentCollection=arguments);
}

//Get settings
return setting;
</cfscript>
</cffunction>
```

This method basically retrieves a setting via the controller and returns it. You can basically create any amount of methods that correspond to your proxy object or just leave the main methods: process() and announceInterception().

