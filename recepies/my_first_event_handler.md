# My First Event Handler

### My First Handler
Below is a simple example of how to create your first event handler. I recommend that you see the Event Handler Guide for more in depth information on how to use ColdBox Event Handlers. This guide expects for you to know the semantics behind event handlers and their basics.

### Sample Code

```js
<cfcomponent name="general" extends="coldbox.system.eventhandler" cache="true" cachetimeout="40" autowire="true">
	
	<cfproperty name="UserService" type="ioc" scope="variables" />
	
	<---  This init is mandatory, including the super.init(). --->
	<cffunction name="init" access="public" returntype="general" output="false">
	    <cfargument name="controller" type="any">
		<cfset super.init(arguments.controller)>
		<---  Any constructor code here. This code will be executed always before any event handler method below. --->
		<cfreturn this>
	</cffunction>
	
	<cffunction name="preHandler" access="public" returntype="void" output="false">
	  <cfargument name="event" type="any">
		<---  Code that gets executed before the event selected for execution in this handler. --->
	</cffunction>
	
	<cffunction name="postHandler" access="public" returntype="void" output="false">
	  <cfargument name="event" type="any">
		<---  Code that gets executed after the event selected for execution gets executed in this handler. --->
	</cffunction>
		
	<cffunction name="dspHello" access="public" returntype="void" output="false">
        <cfargument name="event" type="any">
            <cfset var rc = event.getCollection()>
           
		    <---  EXIT HANDLERS: --->
            <cfset rc.xehDoit = "ehGeneral.doSomething">
	   
			<---  Do Your Logic Here to prepare a view --->
			<cfset event.setValue("welcomeMessage","Welcome to ColdBox!")>	
			<---  Set the View To Display, after Logic. The view's Layout is defined in the config.xml --->
			<cfset event.setView("vwHello")>
	</cffunction>
	
	<cffunction name="doSomething" access="public" returntype="void" output="false">
		<cfargument name="event" type="any">
		<---  collection reference, I am lazy --->
		<cfset var rc = event.getCollection()>
		
		<---  Do Your Logic Here, call to models, etc.--->
	   	<cfset variables.UserService.createUser( rc.fname, rc.lname )>
		
		<---  Set the next event to run, after Logic, this relocates the browser--->
		<cfset setNextEvent("ehGeneral.dspSomething")>
	</cffunction>
	
</cfcomponent>
```

### Overview
As you can see, this event handler contains two public execution methods: dspHello and doSomething, which in turn become ColdBox Events and two implicit events preHandler and postHandler.

> **Infor** All public methods on an event handler become ColdBox Events. 

The dspHello method starts of by setting some EXIT HANDLERS. This just means you declare the event handler method combination to a xehNAME variable that will be used in the view/layout to set as exit points. This way, you can tell exactly where your event handlers can go without looking at the views or layouts and if used in multiple locations, you can do modifications in just one place. This is very important to get used to (best practices), since it can really facilitate the overall flow of the application. So remember to always use EXIT HANDLERS.

The method then sets a variable in the request collection with a message and finally it sets the view to be rendered by the framework using the setView method. How will this view be rendered? Well ColdBox knows the default layout to use since it is declared in the coldbox.xml. However, you might have also declared that the view vwHello must be rendered using the layout: Layout.BigFonts.cfm in the config.xml. Look below for a sample snippet of the configuration file:

```js
<Layouts>
    <--D<eclare the default layout, MANDATORY-->
    <DefaultLayout>Layout.Main.cfm</DefaultLayout>		
		
    <--D<eclare other layouts, with view assignments if needed, else do not write them-->
    <Layout file="Layout.Popup.cfm" name="popup">
        <--Y<ou can declare all the views that you want to appear with the above layout-->
	<View>vwTest</View>
	<View>vwMyView</View>
    </Layout>

    <Layout file="Layout.BigFonts.cfm" name="BigFonts">
        <View>vwHello</View>
    </Layout>
</Layouts>
```

As you can see, the vwHello is defined in the BigFonts layout. So ColdBox already knows how to render the view implicitly. You can also change the layout programatically on the actual event handler. How?? Simple:

```js
<cfset event.setLayout("Layout.Popup")>
<cfset event.setView("vwHello")>
```
By calling the setLayout method. You just overwrote the layout that was implicitly set by your configuration file. So now the view will be rendered using the Layout.Popup.cfm template. Well, not only can you do that, but you can render the view by itself, NO LAYOUT!! WOW!! Truly ColdBox is flexible. How do I do that? 

```js
<cfset event.setView(name="vwHello",nolayout=true)>
```

Voila! The second optional parameter to the setView method is the render by itself flag. By default it is set to false in order to use a layout. However, as you can see, you can override it. After all this is done, the handler gives control back to the ColdBox controller. The ColdBox controller renders the layout, if any, renders the view and finalizes execution.

Now, in the vwHello.cfm we must have a use for that Exit Handler we declared in the event handler; Maybe a link or a form post or whatever. Anyways, lets say it is a form post. Look Below at what vwHello.cfm might have looked like.

```js
<h1> Create a new User</h1>
<cfoutput>
<form method="post" action="index.cfm" name="myform">
<input type="hidden" name="event" value="#rc.xehDoit#">

First Name: 
<input type="text" name="fname" value=""> <br />

Last Name:
<input type="text" name="lname" value=""><br />

<input type="submit" name="submit" value="Create Now!!">
</form>
</cfoutput>
```

As you can see, there is a hidden element called event that has the value of the xehDoit Exit Handler variable. So why did I use rc.xehDoit instead of the typical event.getValue() method. Well, simply because ColdBox creates an RC scope for you to use in layouts and views. It auto-"magically" creates this structure that is a reference to the internal request collection to facilitate variable usage. 

When you submit this form, you will submit the event variable which the framework will retrieve and try to validate. If it validates, it will then instantiate and execute it according to an internal index of registered events. Look at [settings guide](http://wiki.coldbox.org/wiki/ColdboxSettings.cfm) for more information. So you know see that the framework will execute the ehGeneral.doSomething event handler.

This event handler in turn will use the object UserService and call its createUser method and passing two variables from the request collection. The funny looking rc scoped variables is using a local variable called rc that points to the actual request collection in the event object. This is done via the event.getCollection()' method. You can also use the getValue methods, which is more versatile if you need default values and the sort. If you need to check the existence of a request collection variable and set a default if not found, then use the paramValue method. If you just want to check for existence, then use the valueExists method. Once that user is created, then you have a setNextEvent method call and passing another event handler method combination. This line of code in essence, relocates the browser to the next event to execute. If no event is passed to the setNextEvent like below:

```js
<cfset setNextEvent()>
```

Then the framework will relocate to the default event set in the coldbox.xml.

### The Caching Parameters

Since ColdBox is built with a solid object cache foundation, your handlers can also be cached if needed. You will do this by adding two meta data attributes to the cfcomponent tag. By default handlers WILL BE cached, unless you specifically use the cache meta data attribute to tell the framework NOT to cache it. Caching of handlers simulates persistence, so remember this if you are planning handlers that can maintain their own persistence. This is a true flexible and awesome feature. Persistence controlled by the framework for you.

|Attribute|Type|Description|
|--|--|--|
|cache|boolean|A true or false will let the framework know whether to cache this handler object or not. |
|cachetimeout|numeric|The timeout of the object in minutes. This is an optional attribute and if it is not used, the framework defaults to the default object timeout in the cache settings. You can place a 0 in order to tell the framework to cache the handler for the entire application timeout controlled by coldfusion. |
|cacheLastAccessTimeout|numeric|The last access timeout of the object in minutes if using last access timeouts. This is an optional attribute and if it is not used, the framework defaults to the default last object timeout in the cache settings. |

```js
<cfcomponent name="ehGeneral" output="false" cache="true" cacheTimeout="45" cacheLastAccessTimeout="10">

</cfcomponent>
```

### The Autowire Parameeter

ColdBox tightly integrates with !ColdSpring and comes bundled with LightWire so you can manage your model via these dependency injection frameworks. Due to this, the framework can help you wire up your handlers with these beans by doing the following:

1. Make sure the autowire interceptor has been declared in your configuration file. See [Autowire Guide](http://wiki.coldbox.org/wiki/Interceptors:Autowire.cfm)
2. Add the autowire metadata attribute to your handler and set it to true
3. Create cfproperty tags to annotate your handler with the beans you want injected or create setter methods.

In our example, we add the autowire attribute and set it to true:

```js
<cfcomponent name="ehGeneral" output="false" cache="true" cacheTimeout="45" cacheLastAccessTimeout="10" autowire="true">

</cfcomponent>
```

We then do cfproperty annotations:

```js
<cfproperty name="UserService" type="ioc" scope="variables" />
```

For more on how to write these annotations, please visit the Autowire Guide. To summarize, this annotation tells ColdBox to ask the IoC container for a bean named UserService and inject it into the variables scope. After handler creation, your handler will be autowired with these services.
