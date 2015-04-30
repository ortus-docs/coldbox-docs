# Test Harness

Every ColdBox application template comes with a nice test harness inside of a `tests` folder.

![](../images/testharness.png)

This test template or test harness includes folders for major test classes you will be creating, Eclipse & ANT connection resources, ANT scripts and so much more. This is a great starting point for your application testing adventure. The most important piece of your test harness is the Application.cfc file. Please note that your testing harness must have a different Application space than your normal ColdBox application. You do not want your tests to alter the real application and vice versa. So in this Application.cfc you will try an mimic all the real application's session, client, scopes, ORM, S3, settings, etc. The trickiest part to setup is ORM integration. You have to make sure that your paths and entities can be located by your tests which run in a separate application space.

Basic Application.cfc: 

```js
<cfcomponent output="false">
	<cfset this.name = "ColdBoxTestingSuite" & hash(getCurrentTemplatePath())> 
	<cfset this.sessionManagement = true>
	<cfset this.sessionTimeout = createTimeSpan(0,0,30,0)>
	<cfset this.setClientCookies = true>
	
</cfcomponent>
```

More Complex Application.cfc: 

```js
<cfcomponent output="false">
	<cfset this.name = "ContentBoxTestingSuite" & hash(getCurrentTemplatePath())>
	<cfset this.sessionManagement = true>
	<cfset this.sessionTimeout = createTimeSpan(0,0,0,30)>
	<cfset this.setClientCookies = true>

	<cfscript>
	// ORM Settings
	this.ormEnabled = true;
	// FILL OUT: THE DATASOURCE OF CONTENTBOX
	this.datasource = "contentbox";
	// FILL OUT: THE LOCATION OF THE CONTENTBOX MODULE
	rootPath = replacenocase(replacenocase(getDirectoryFromPath(getCurrentTemplatePath()),"test\\",""),"test/","");

	this.mappings["/root"]   = rootPath;
	this.mappings["/contentbox-test"] 	= getDirectoryFromPath(getCurrentTemplatePath());
	this.mappings["/contentbox"] 		= rootPath & "/modules/contentbox" ;
	this.mappings["/contentbox-ui"] 	= rootPath & "modules/contentbox-ui";
	this.mappings["/contentbox-admin"] 	= rootPath & "modules/contentbox-admin";
	this.mappings["/contentbox-modules"] = rootPath & "modules/contentbox-modules";
	this.mappings["/coldbox"] 			= rootPath & "/coldbox" ;

	this.ormSettings = {
		cfclocation=["../modules/contentbox"],
		logSQL 				= true,
		flushAtRequestEnd 	= false,
		autoManageSession	= false,
		eventHandling 		= true,
		eventHandler		= "contentbox.model.system.EventHandler",
		skipCFCWithError	= true,
		secondarycacheenabled = true,
		cacheprovider		= "ehCache"
	};

	public boolean function onRequestStart(String targetPage){
		// ORM Reload
		ormReload();

		return true;
	}
	</cfscript>
</cfcomponent>
```

