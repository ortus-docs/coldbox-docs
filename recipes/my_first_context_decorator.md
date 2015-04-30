# My First Context Decorator

## Introduction

In order to understand this quick hands on guide, you must first understand what a decorator is and how it applies to ColdBox. For that, please read the Request Context Decorator Guide. That guide will show you why use a decorator and how. This guide is just a quick hands on that will show you the following:

1. How to declare your decorator
2. How to create your decorator
3. Creating some methods

### How to declare your decorator

In order for ColdBox to know that it must use a decorator for your request context, it must be declared in your ColdBox ConfigurationCFC as shown below:

```js
coldbox.requestContextDecorator = "model.MyRequestContext";
```

The value of the setting is the instantiation path of the object, just like if you where using the CreateObject() method, so you can use mappings, paths, etc.

That's it!! What? You thought there was more to it. Well, no. You declare, and ColdBox uses.

### How to create your decorator

The second step is to create the component in the path specified by the value in your setting. In this case its in model/myRequestContext.cfc. Below you can see my declaration:

```js
<cfcomponent name="myRequestContext"
             hint="This is a simple custom request context"
             output="false"
             extends="coldbox.system.web.context.RequestContextDecorator">



</cfcomponent>
```

So all you need to create the request context is to actually make it inherit from the ColdBox base RequestContextDecorator class. That's it, then you will have all the methods needed for your decorator to work.

Some special methods that you inherit for this decorator are:

|Method|Description|
|--|--|
|getRequestContext() |This methods gives you the original request context object you have decorated |
|configure() |A pseudo-constructor method that get's executed right after a request has been captured |
|getController()	|Get a reference to the injected ColdBox Controller |

### Creating some methods

Now that I have declared and constructed the shell for my decorator, I will create some methods.

##### The Configure method

You can create a special method called configure in your decorator that will be fired every time the context gets created. This is a great way to create a pseudo-constructor on your object. Look at mine below, in which I trim all the incoming variables in the context:

```js
 <cffunction name="Configure" access="public" returntype="void" hint="This is the configuration method for your context as its created." output="false" >
	<cfset var key = "">
	<---  Get the original collection via the original request context object and call a non-decorated method --->
	<cfset var rc = getRequestContext().getCollection()>

	<---  I will trim all of the request collection --->
	<cfloop collection="#rc#" item="key">

		<---  Trim only simple values --->
		<cfif isSimpleValue(rc[key])>
			<cfset rc[key] = trim(rc[key])>
		</cfif>

	</cfloop>
</cffunction>
```

Is that cool or what. I have just trimmed all the variables that get captured by the framework into the request collection.

###### getValue decorated

The method that I create next is the getValue method. This will actually decorate the original getValue method with the functionality of actually returning an empty value if the value doesn't exist.

```js
<cffunction name="getValue" returntype="Any" access="Public" output="false">
<---  ************************************************************* --->
<cfargument name="name"         type="string"   required="true">
<cfargument name="defaultValue" type="any"      required="false" default="NONE">
<---  ************************************************************* --->
<cfscript>
	var originalValue = "";

	//Check if the value exists
	if( valueExists(arguments.name) ){
		//Get the original value
		originalValue = getRequestContext().getValue(argumentCollection=arguments);
	}

	//Return either the value or an empty string.
	return originalValue;
</cfscript>
</cffunction>
```



###### An extra method: getSESSelf()

```js
<cffunction name="getSESSelf" returntype="string" access="Public" output="false">
<cfscript>
var self = "index.cfm/";

self = self & getCurrentHandler() & "/" & getCurrentAction();

return self;
</cfscript>
</cffunction>
```

This basically constructs the following:

```js
index.cfm/{handler}/{action}
```

Very useful on navigation:

```js
<a href="#event.getSESSelf()#">My Link</a>
```

And that is it my folks, you have a working decorator. One last important note, is that by calling the getController() method, you have access to your application settings, plugins, interceptors and so much more. So you can build even more powerful request context objects.
