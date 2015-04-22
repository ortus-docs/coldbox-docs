# Object factory custom plugin

<small>Author: Sana Ullah & Luis Majano</small>

### Introduction

Developing an object factory in ColdBox is really easy and it will also give you great benefits. In this small tutorial , I will cover building an object-factory plugin as a singleton pattern implementation. 

### Overview

ColdBox plugins are simple but they can be very powerful. You can develop many application functionalities independently from your domain model. For more information about ColdBox custom plugin please see [wiki:cbMyFirstPlugin My First Plugin Guide]. Below are some of the most important points about Custom Plugins:

* The beauty of ColdBox custom plugins are that you don’t need to initialize the plugin.
* You can access all of your colbox.xml.cfm settings in your custom plugin.
* You are also able to access the ColdBox controller methods like `getController.getAppRootPath(`) etc. I would recommend to see the ColdBox API for more detail at http://www.coldbox.org/api/
* ColdBox OCM caching features are also available in all custom plugins, so we can take advantage of ColdBox's caching facilities.
* It is very easy to call custom plugins

### Calling the custom Factory

```js
controller.getPlugin(plugin = "ObjectFactory", customPlugin = true, [newInstance = false])
//or
getMyPlugin(plugin="ObjectFactory")
```

* newInstance = false : This will tell ColdBox to get a new Instance of the Object or an already instantiated version of the object from cache. I would recommend to set it false, as we will have own way to tell our object-factory plugin to return new instance or cached version.


### Plugin Code

Here is the plugin
```js
<cfcomponent name="ObjectFactory"
			 hint="An Object Factory plugin."
			 extends="coldbox.system.plugin"
			 output="false"
			 cache="true"
                         cacheTimeout="0">

	<cffunction name="init" access="public" returntype="ObjectFactory" output="false">
		<cfargument name="controller" type="any" required="true">
		<cfset super.Init(arguments.controller) />
		<cfset setpluginName("Object Factory") />
		<cfset setpluginVersion("1.0") />
		<cfset setpluginDescription("A Object Factory plugin. It will load object based on lazy-loading tech.") />
		
		<cfscript>
		variables.ObjectName		= StructNew();
		variables.ObjectHolder		= StructNew();
			
		// your own settings structure. 		
		variables.Mapping = getSetting("Mapping");
		variables.dsn	  = getDatasource("MyDB").getalias();
			
	        // This could be your list of service-controller/manager-objects/etc	
		ObjectName["objGalleryM"] = '#variables.Mapping#.model.GalleryManager';
		ObjectName["objPhotoM"]	  = '#variables.Mapping#.model.PhotoManager';
		ObjectName["objBlogM"]    = '#variables.Mapping#.model.BlogManager';
		</cfscript>
		
		<cfreturn this />
	</cffunction>
	
	 
	<cffunction name="getObject" access="public" returntype="Any" hint="Return initiated object" output="false">
		<---************************************************************** --->
		<cfargument name="Name"	type="string" 	required="true" hint="Name of Object">
		<cfargument name="newInstance"	type="boolean" 	required="false" default="false" hint="new-object-instance or not">
		
		<cfif structKeyExists(ObjectHolder,arguments.Name) and not arguments.newInstance>
			<---  if object is intialised and stored then return --->
			<cfreturn ObjectHolder[arguments.Name] />
		<cfelse>
			<---  if object is not intialised or requested as new-instance --->
			<cflock name="#arguments.Name#Loading" timeout="2">
				<---  
					coldbox OCM caching is available, 
					you can get any stored objects and pass to your service-controller/manager/etc
					 
					Certainly you can extend up to your requirements	
				--->
				
				<cfset ObjectHolder[arguments.Name] = CreateObject("component","#ObjectName[arguments.Name]#").init(variables.dsn) />
				<cfreturn ObjectHolder[arguments.Name] />
			</cflock>	
		</cfif>
		
	</cffunction>
</cfcomponent>
```

The code below will show you how to call the custom plugin to retrieve an object from the object factory.

```js
getMyPlugin("ObjectFactory").getObject(Name=ObjectName,newInstance=false);
```

And there you go, a fully working object factory or service locator plugin.




