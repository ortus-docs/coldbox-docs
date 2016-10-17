# My First Custom Plugin

### Introduction

ColdBox supports custom plugins created by YOU! You can now extend the power of the framework to any application specific task that you would like to do. How? Well, all you need is to create the plugin CFC in your plugins convention folder or in your external plugins location folder. After that its just a matter of calling it from anywhere you want. This guide will show you how, but if you have not read the Plugins Guide I recommend you read it first.

> **Important** Any CFC can be a ColdBox plugin and it can exist anywhere in the server. [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) creates plugins since version 3.5 and gives us tremendous flexibility. Inheritance gives you tigther integration to the framework if needed.

### Creating the CFC

The first step to creating your own plugin would be to plan it and decide what you need to do (Your job, not mine!). Based on that first step, the second step is to create the CFC. Below is a hello world plugin template.

```js
<cfcomponent name="hello" hint="This is a hello plugin" extends="coldbox.system.Plugin" output="false" cache="true" cachetimeout="20">

<-------------------------------------------- CONSTRUCTOR ------------------------------------------->

<cffunction name="init" access="public" returntype="hello" output="false">
  <cfargument name="controller" type="any" required="true">
  <cfset super.Init(arguments.controller) />
  <cfset setpluginName("Hello Plugin")>
  <cfset setpluginVersion("1.0")>
  <cfset setpluginDescription("This is a hello plugin.")>
  <cfset setPluginAuthor("Luis Majano")>
  <cfset setPluginAuthorURL("www.luismajano.com")>

  <---  Any constructor code you want --->

  <cfreturn this>
</cffunction>

<-------------------------------------------- PUBLIC ------------------------------------------->

<cffunction name="sayhello" output="false" access="public" returntype="string" hint="Say hello">
  <cfreturn "Hello there, my name is #getPluginName()# #getPluginVersion()#">
</cffunction>

<cffunction name="sayHelloFromEvent" output="false" access="public" returntype="string" hint="Say hello from event object">
  <---  Get Event Context --->
  <cfset var event = getRequestContext()>
  <---  Return the name in the event context --->
  <cfreturn "Hello there, my name is #event.getValue("name","")#">
</cffunction>

<-------------------------------------------- PRIVATE ------------------------------------------->


</cfcomponent>
```

### Where do I save it?

You can place your plugin in the plugins directory or in the external location's directory via the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) setting PluginsExternalLocation. However, you can potentially place a Plugin anywhere in your server.

```js
// Singleton plugin
component singleton{}
// Cached plugin
component cache="true" cacheTimeout="45"{}
// Granular Cached plugin
component cache="true" cacheTimeout="45" cacheLastAccessTimeout="15"{}
// Transient
component{}
```

### The Inheritance

The plugin inheritance is completely optional. If you do, you will inherit from our base plugin class: coldbox.system.Plugin which will give you access to tons of acces to framework related methods and the framework super type. So check out the [API Docs](http://apidocs.coldbox.org/) for the latest methods you can use here.


### Your Methods

You have now seen what your plugin object is and what its parents are. So you can now build private/public methods that will make your plugin specific. So code away and create your own methods...

Below is a sample method:

```js
<cffunction name="hello" output="false" access="public" returntype="string" hint="Say hello">
<cfreturn "Hello there, my name is #getpluginName()# #getpluginVersion()#
			 and I am running on #getSetting("codename",true)# version #getSetting("version", true)#">
</cffunction>
```

> **Important** One thing you can count on for any plugin is that the init() method, which is the constructor, will always be called when the plugin is created.

### Getting access to the request context

You also have the ability to get the request context from within a plugin, all you need to do is use the following code:

```js
<---   LOng Approach --->
<cfset var event = getController().getRequestService().getContext()>
<---  Facade Method --->
<cfset var event = getRequestContext()>
```

This asks the request service to give you the current request context. You can then manipulate the event object as needed.

### How to call the plugins?

Now that you have created your plugin, you are ready to use it. You can call it from your event handlers/layouts/views or even other plugins by using the following methods:

* getPlugin(plugin='plugin name',customPlugin=true, [newInstance])
* getMyPlugin(plugin='plugin name', [newInstance])

So if you need to get a custom plugin, just pass the custom plugin boolean flag as true to the getPlugin() method. The newInstance argument, tells the plugin factory to give you a new fresh instance of that plugin. If not, you could be potentially getting a cached plugin. The default value is false.

Below is a sample:

```js
<cfscript>
myPlugin = getPlugin(plugin="hello",customPlugin=true);
myPlugin.sayHello();
//or you can even do
event.setValue("My Hello", getPlugin("hello",true).sayHello() );
</cfscript>

//or
<cfoutput>#getPlugin("hello",true).sayHello()#</cfoutput>

<---  GET MY PLUGIN SYNTAX --->
<cfscript>
myPlugin = getMyPlugin("hello");
myPlugin.sayHello();
//or you can even do
event.setValue("My Hello", getMyPlugin("hello").sayHello() );
</cfscript>

//or
<cfoutput>#getMyPlugin("hello").sayHello()#</cfoutput>
```

### Conclusion

As you can see from this example, ColdBox Plugins are extremely flexible and easy to build. You can extend the framework to any application specific task you need, you can build security filters, AJAX widgets, extend the core plugins, and much more. This is giving true power to the developer to extend the framework not only programmatically but visually. So get to it!

