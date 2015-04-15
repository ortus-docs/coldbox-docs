# My First Interceptor

### Introduction

This hands on guide will show you how to quickly create a ColdBox interceptor. If you are still not that familiar with ColdBox interceptors, I suggest you read the Interceptors Guide. This guide is just a quick hands on that will show you the following:

1. How to declare your interceptor
2. How to create your interceptor
3. Creating some methods

### How to declare you interceptor

In order to use an interceptor you must first declare it on your ColdBox configuration file under the Interceptors array element. Each interceptor declaration is a structure with the following elements:

* class : The instantiation path of the Interceptor CFC
* name : The unique name of the CFC, if not passed, it uses the name of the CFC
* properties : A structure of configuration properties for the CFC

```js
interceptors = [
  {class="model.interceptors.xmlInjector", properites={
   xmlFile = "config/myCustomXML.xml",
   varName = "myCustomXML" }
  }
];
```

> **Note** Interceptors can also be registered at runtime by using the provided API in the interceptor service object.

### Creating the interceptor

Below you will see the code needed to declare a ColdBox interceptor:

```js
<cfcomponent name="xmlInjector"
	hint="This interceptor will read an xml file, parse it and inject it to the settings."
	output="false"
	extends="coldbox.system.Interceptor">

</cfcomponent>
```

That's it. You have now declared an interceptor, it just needs to inherit from the base ColdBox Interceptor class, which is completely optional.

> **Important**  All Interceptors are persisted as singleton objects. Meaning one instance for the entire application. 

All interceptors will inherit all the methods found in the framework super type object, which let's you tap into the ColdBox Life Cycle. You can see a listing of those methods in the (See the Live API for the latest updates)

|Method|Return Type|Description|
|--|--|--|
|configure|void|Used to configure your interceptor after instantiation|
|getProperties|struct|Get the entire properties structure|
|getProperty|any|Get a property by name|
|setProperty |void|Set a property in the properties structure |
|propertyExists |boolean|Returns if the property name sent in exists or not. |
|unregister|void|Unregister this interceptor from an interception state |

### Creating Some Methods

So now lets start creating some cool methods. The first method we will create is the configure method. 

##### The configure method

In our case, this method checks that all properties are set, set a custom property and throw errors.

```js
<cffunction name="Configure" access="public" returntype="void" hint="This is the configuration method for your interceptor" output="false" >
<cfscript>
	var theFile = "";
	
	//verify the properties declared in the configuration file.
	if ( not propertyExists('xmlFile') ){
		throw("The property xmlFile does not exist.");
	}
	if ( not propertyExists('varName') {
		throw("The property varName does not exist.");
	}
	
	//Get the File Path
	theFile = getController().getAppRootPath() & getProperty('xmlFile');
	
	if ( fileExists(theFile) ){
		//Save it in the properties
		setProperty('expandedXMLFile', theFile);
	}
	else{
		throw("The xml file: #thefile# does not exist. Please review your paths");
	}
	
</cfscript>
</cffunction>
```

As you can see from the code, you do some property tests, if all are successful and the file exists, then set the expanded path in the properties of the interceptor.

### The afterConfigurationLoad method

Interceptors work by you implementing the core execution points that you can find in the Interceptor Chapter. Below we implement the afterConfigurationLoad method. This methods executes after the framework loads and the config file has been parsed and loaded. Please note that not only can you intercept core framework execution points, but you can make up your own by using custom interception points. This gives you the ability to use a methodology of broadcast-listener approaches.

