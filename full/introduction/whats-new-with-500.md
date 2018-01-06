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

The SES interceptor now has a boolean flag to denote if rewrites are turned on/off and you will no longer set a base URL.  We will automatically detect the base URLs according to multi-domain hosting.  Meaning you can out of the box create multi-tenant applications with ease.

```java
setFullRewrites( true )
```

[COLDBOX-577] - Add Boolean for Rewrites in the SES Interceptor: setFullRewrites( [true] )
[COLDBOX-647] - New context method: getCurrentRouteName() to get the current route name
[COLDBOX-646] - No need to declare the baseURL on routing, this is auto-detected and multi-sub-domain hosting enabled
[COLDBOX-616] - Module support for Resourceful routes
[COLDBOX-588] - Resourceful Routes
[COLDBOX-265] - Allow named routes


# Event Execution

[COLDBOX-579] - You can return the `event` object from handlers and it will now be ignored for simple evaluations
[COLDBOX-636] - Deprecate setNextEvent() in favor of relocate()
[COLDBOX-595] - new request context method getOnly() to allow for retreival of certain keys from request contexts
[COLDBOX-303] - Enhance cache suffix property to allow closures for dynamic event caching suffixes
[COLDBOX-284] - Choose Provider for event caching Via Annotations



## Rendering
[COLDBOX-600] - Expose view path to Coldbox API
[COLDBOX-622] - Custom Object Marshalling Convention $renderdata on handler results
[COLDBOX-621] - When returning complex data from handlers, ColdBox will auto marshall to JSON
[COLDBOX-620] - Action specific renderdata annotation support
[COLDBOX-606] - New request context method: sendFile() taken from file utils so you can deliver files directly from handlers
[COLDBOX-569] - Rendering Named Regions
[COLDBOX-568] - New interceptor after Renderer init: afterRendererInit
[COLDBOX-36] - Content Negotiation using the Accept header via the SES interceptor




## Testing

[COLDBOX-591] - Move Testing Lifecycle Methods to annotation methods to prevent accidental overriding
[COLDBOX-598] - Reset the response when calling setup in integration testing to avoid duplicate headers
[COLDBOX-567] - Base test case doesn't allow inherited annotations
[COLDBOX-643] - Add `getRenderData` and `getStatusCode` helpers to Integration Testing


## HTML Helper

[COLDBOX-599] - HTML Helper table() should distinguish between array of objects and array of entties
[COLDBOX-639] - HTMLHelper Option to put wrap <input> with <label> instead of closing <label> first
[COLDBOX-640] - HTMLHelper mechanism to add non-data attributes to labels
[COLDBOX-545] - wrapTag should accept attributes (class, id, whatever)


