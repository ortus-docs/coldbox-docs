# Caching objects

The sample you will see below consists of three parts: 

1. Setup of an object in cache
2. Testing of the object in cache
3. Retrieving the object in cache.

Remember that the ColdBox Object Cache Manager is available to you from any execution point. You only need to use the following snippet to have access to it:

```js
getColdboxOCM();
```

or

```js
getController().getColdboxOCM();
```

As for all the functionality of this wonderful caching system, please look at the [[ColdboxCache | ColdBox Cache] and at the latest [CFC Api](http://www.coldbox.org/api).

> **Important** The ColdBox cache is a java/coldfusion hybrid and therefore it is CASE SENSITIVE! 

### Step 1: Step of an object in cache

The actual first setup of an object can occur in any point in time, but for this example I will do it in the onAppInit method of my application, which has been declared in my **coldbox.xml.cfm**. This method executes on every application start. 


```js
<cffunction name="onAppInit" access="public" returntype="void" output="false">
  <cfargument name="Event" type="coldbox.system.beans.requestContext">
  <cfset var MyServicePath = "mycomponents.generator.GeneratorService">
  <cfset var GService = CreateObject("component",MyService).init(getSetting("adminpass"))>

  <---  Place object in object cache and persist it with a default Framework Timeout. --->
  <cfset getColdboxOCM().set("GService",GService)>
</cffunction>
```


