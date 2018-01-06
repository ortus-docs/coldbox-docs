# What's New With 5.0.0

ColdBox 5.0.0 is a major release for the ColdBox MVC platform.  It has been long standing as we have been learning so much especially around containerization and portability.  This release has over 70 key issues addressed from new features, improvements and bug fixes.  So let's begin our ColdBox 5 adventure.

## Engine Deprecation

It is also yet another source code reduction due to the dropping of support for the following CFML Engines:

* Adobe ColdFusion 9
* Adobe ColdFusion 10

That's right, you will need Adobe ColdFusion 11+ or Lucee 4.5+ in order to work with ColdBox 5.

## Automation

We have fully automated all build processes with ColdBox 5 to include CommandBox and TestBox testing, Travis integration and a fully automated test suite that executes against **ALL** supported CFML engines.  Our code coverage has increased due to this work dramatically.  We discovered engine bugs that must have plagued our users for years.  YAY for testing!

## Performance Improvements

As we update core files we keep optimizing the source code and migrating to full cfscript.  This migration has allowed us to optimize very old code into modern times with significant performance gains.  We have also moved from internal Java reflection to get file information to native CFML functions since now all engines support it.  Just this alone has improved vanilla requests tremendously.

WireBox object creation and manipulation has also increased due to new locking strategies in our Mixer Util.  You will especially see the difference when creating many transient objects.

## Lucee Full Null Support

Thanks to the community we have now full `null` support for the Lucee CFML engine and up-coming Adobe 2018.

## Core Framework Exception Handling

The core framework has been revised with a fine tooth comb to provide better exception messages, better helpful messages and also the ability to intercept exceptions at the framework level via normal exception handlers.  You will also see that ColdBox can detect response headers now and make sure it can avoid caching exceptions when event caching is turned on.  The appropriate status code will now be reported.

You will also find in the log files attempts to reinit the framework with invalid or missing passwords.

## Containers + Environments Support

ColdBox introduces two new methods that are available for your `ColdBox.cfc` and your `ModuleConfig.cfc` objects:

* `getSystemProperty( name, defaultValue )` - Retrieve a Java System property
* `getSystemSetting( name, defaultValue )` - Discover an environment variable either by searching system properties first and then system environment variables second.

> **Hint**  These methods are also found in the ColdBox core utility object: `coldbox.system.core.util.Util` which can be injected anywhere it is needed.

These methods will allow you to interact with docker environment variables and override settings, add new settings, and much more.


## Modules, Modules and more Modules

We continue to innovate in the Hierarchical MVC (HMVC) design of ColdBox by fine-tuning the modular services and interactions.  Here are the major updates to modules in ColdBox 5.

* All module interceptors are now namespaced to avoid name conflicts with other modules
* New modules injected variable: `coldboxVersion` to be able to quickly detect what version of ColdBox they are running under. This will allow you to create modules that can respond to multiple ColdBox versions.

### Inherited Entry Point

All modules have had a URL entry point since the beginning: `this.entryPoint = "/route"`.  This entry point is registered in the URL mappings to allow for a unique URL pattern to exist for specific modules.  That is great!  However, in modern times and with the amount of API centric applications that we are building we wanted to introduce an easier way to build resource centric APIs.

What if our resource URLs could match by convention our module inceptions? Well, with the new inherited entry points, you can do just that.  By leveraging HMVC and module inception you can now create automatic URL nesting schemas.

You can turn this on just by adding the following flag in your `ModuleConfig.cfc`:

```java
this.inheritEntryPoint = true
```

When the module is registered it will recurse its parent tree and discover all parent entry points and assemble the entry point accordingly.  Let's see an example. We are working on an API and we want to create the following URL resource: `/api/v1/users`. We can leverage HVMC and create the following modular structure:

```
+ modules_app
  + api
    + modules_app
      + v1 
        + modules_app
          + users
```

This models our URL resource modularly.  We can now go into each of the module's `ModuleConfig` and add only the appropriate URL pattern and turn on inherit entry point.

```
api   - this.entryPoint = /api
v1    - this.entryPoint = /v1
users - this.entryPoint = /users
``` 

This feature will not only help you identify API routing, but help you build with modularity in mind.  You will also find a new method in the request context called `getModuleEntryPoint()` to retrieve a modules inherited entry point, which is great for URL building.

### New Module Events

You can now listen to more events of the module life-cycle:

- `preModuleRegistration` - before each module is registered
- `postModuleRegistration` - after each module is registered
- `afterModuleRegistrations` - This will fire when all modules have been registered.  This is great if you want modules to dynamically depend on each other.
- `afterModuleActivations` - This will fire when all modules have been succesfully activated. Great for caching updates, announcements, etc.

### Modules_app convention for inception
We have now added the `modules_app` convention for all module inceptions.

### Default Model Export Convention

If you create a module where there is a model with the same name, then we will automatically map it in Wirebox as `@modulename`.

```java
cors = getInstance( "@cors" )
```

This is great for 1 model modules.

## Routing Enhancements

We continue to push forward in making ColdBox the best RESTFul framework for ColdFusion (CFML).  In ColdBox 5 we have great new additions for both routing and rendering.

### Simplified URL Rewrites

The SES interceptor now has a boolean flag to denote if rewrites are turned on/off and you will no longer set a base URL.  We will automatically detect the base URLs according to multi-domain hosting.  Meaning you can out of the box create multi-tenant applications with ease.  We will also be adding subdomain routing in our final release.

```java
setFullRewrites( true ); // defaults to false.
```

### Named Routes

This has been a long-time coming to ColdBox and I can't believe we had never added this before.  Well, named routes are here, finally!

If you have used other frameworks in other languages, they allow you to name your routes so you can build links to them in an easier manner.  We have done just the same.  The `addRoute()` method accepts the `name` parameter and we have extended the request context with some new methods:

- `getCurrentRouteName()` - Gives you the name of the current route, if any
- `route()` - Allows you to build URLs according to named routes

**Define the Route**
```java
// Create a named route
addRoute(
  pattern  = "/user/:username/:page",
  name     = "user_detail"
);

// Same for modules
routes = [

  { 
    pattern  = "/detail/:username/:page",
    name     = "user_detail"
  }
  
];
```

**Build Links**
```
<a href="#event.route( name="user_detail", params={ username="luis", page="1" } )#>User Details</a>
```

The `route` method signature can be seen below:

```java
/**
* Builds links to named routes with or without parameters. If the named route is not found, this method will throw an `InvalidArgumentException`.
* If you need a route from a module then append the module address: `@moduleName` in order to find the right route.
* 
* @name The name of the route
* @params The parameters of the route to replace
* @ssl Turn SSL on/off or detect it by default
* 
* @throws InvalidArgumentException
*/
string function route( required name, struct params={}, boolean ssl )
```

### Resourceful Routes

In ColdBox 5, you can register resourceful routes to provide automatic mappings between HTTP verbs and URLs to event handlers and actions by convention.  By convention, all resources map to a handler with the same name or they can be customized if needed.  This allows for a standardized convention when building routed applications.

You will now have a new `resources()` method in the SES interceptor or a `resources` struct in your modules.  Yes, all modules can have their own resourceful routes as well.

```java
// Creates all resources that point to a photos event handler by convention
resources( "photos" );

// Register multiple resources either as strings or arrays
resources( "photos,users,contacts" )
resources( [ "photos" , "users", "contacts" ] );

// Register multiple fluently
resources( "photos" )
	.resources( "users" )
	.resources( "contacts" );

// Creates all resources to the event handler of choice instead of convention
resources( route="photos", handler="MyPhotoHandler" );

// All resources in a module
resources( route="photos", handler="photos", module="api" );

// Resources in a ModuleConfig
resources = [
  { resource="photos" },
  { resource="users", handler="user" }
];
```

This single resource declaration will create all the necessary variations of URL patterns and HTTP Verbs to actions to handle the resource. It will even create named routes for you.

![](/assets/resourceful_routes.PNG)

For in-depth usage of the `resources()` method, let's investigate the API Signature:

```java
/**
* Create all RESTful routes for a resource. It will provide automagic mappings between HTTP verbs and URLs to event handlers and actions.
* By convention, the name of the resource maps to the name of the event handler.
* Example: `resource = photos` Then we will create the following routes:
* - `/photos` : `GET` -> `photos.index` Display a list of photos
* - `/photos/new` : `GET` -> `photos.new` Returns an HTML form for creating a new photo
* - `/photos` : `POST` -> `photos.create` Create a new photo
* - `/photos/:id` : `GET` -> `photos.show` Display a specific photo
* - `/photos/:id/edit` : `GET` -> `photos.edit` Return an HTML form for editing a photo
* - `/photos/:id` : `POST/PUT/PATCH` -> `photos.update` Update a specific photo
* - `/photos/:id` : `DELETE` -> `photos.delete` Delete a specific photo
* 
* @resource 		The name of a single resource or a list of resources or an array of resources
* @handler 		The handler for the route. Defaults to the resource name.
* @parameterName 	The name of the id/parameter for the resource. Defaults to `id`.
* @only 			Limit routes created with only this list or array of actions, e.g. "index,show"
* @except 			Exclude routes with an except list or array of actions, e.g. "show"
* @module 			If passed, the module these resources will be attached to.
* @namespace 		If passed, the namespace these resources will be attached to.
*/
function resources(
  required resource,
  handler=arguments.resource,
  parameterName="id",
  only=[],
  except=[],
  string module="",
  string namespace=""
)
```



## Event Execution

We have also done several update for event executions, event caching and overall MVC operations:

* You can now return the `event` object from event handlers and the framework will not fail.  It will be ignored.
* `setNextEvent()` is now deprecated in favor of a `relocate()` method.
* Request context has a `getOnly( keys, private=false )` method to allow for retrieval of certain keys only from the public or private request contexts. Great for functional programming.

### Event Caching Enhancements

#### Dynamic Cache Suffix
You can now leverage the cache suffix property in handlers to be declared as a closure so it can be evaluated at runtime instead of a static prefix.

```java
this.EVENT_CACHE_SUFFIX = function( eventHandlerBean ){
  return "a localized string, etc";
};
```

With this ability you can enable dynamic cache suffixes according to runtime environments on a per user basis.

#### Cache Provider Annotations

You can now add a `cacheProvider` annotation to your cache enabled functions and decide to what CacheBox provider the output will be cached too instead of the default provider of `template`:


```java
function index( event, rc, prc ) cache=true cacheProvider=couchbase{

}
```

## Rendering Enhancements

We have also done tremendous updates to the rendering engines in ColdBox 5, especially for RESTFul responses and content negotiation.

* ColdBox now detects the `rc.format` not only to incoming URL extensions, but also via the `Accept` Header as content-negotiation.
* New interception point `afterRenderInit` which will allow you to add your own injections to the main ColdBox renderer and modify the renderer at runtime.
* The request context can now deliver files to users via our `sendFile()` method.d

### Native JSON Responses + Handler Auto-Marshalling

JSON is the native response now for event handlers that return complex variables.

```java
function data( event, rc, prc ){
  return [1,2,3];
}

function data( event, rc, prc ){
  return myservice.getQuery();
}
```

That's it! If you return a complex variable from an event handler, ColdBox will convert it to JSON for you automatically.  We will even inspect the return object and if it has a `$renderdata()` method, we will call it for you and return your very own marshalled data! But there's more.  You can add a `renderData` annotation to your action and add any valid `renderdata()` type and it will return it accordingly.

```java
function data( event, rc, prc ) renderdata="xml"{
  return [1,2,3];
}

function data( event, rc, prc ) renderdata="pdf"{
  event.setView( "users/index" );
}
```

You can even add a default response type by adding the `renderdata` annotation to the `component` construct:

```java
component renderdata="json"{

}
```

### Named Regions


[COLDBOX-622] - Custom Object Marshalling Convention $renderdata on handler results
[COLDBOX-621] - When returning complex data from handlers, ColdBox will auto marshall to JSON
[COLDBOX-620] - Action specific renderdata annotation support

[COLDBOX-569] - Rendering Named Regions






## Testing Enhancements

Here are some of the major updates for integration testing with ColdBox and TestBox:

* Reset the response when calling `setup()` in integration testing to avoid duplicate headers within same request executions.
* Base test case doesn't allow for inherited annotations.  Now it does since we moved the testing life-cycle methods to annotation based instead of by name.
* Added dynamic methods `getRenderData()` and `getStatusCode()` helpers to the request context upon `execute()` execution.  This will allow you a shorthand approach to getting response status codes and response rendering data struct.



