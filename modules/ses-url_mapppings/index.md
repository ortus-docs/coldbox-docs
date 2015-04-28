# SES-URL Mapppings

SES Routing is the default routing and execution mechanisms for ColdBox. Creating URL Mappings has great benefits and incredible extensibility. If you are not familiar with them, please review our [URL Mappings & SES section](http://wiki.coldbox.org/wiki/URLMappings.cfm). We reviewed that you can have custom routes for each module, and that is fantastic. However, in order for the routes to become active you must add an entry point in the module configuration object so that entry point can be registered for you in the host application.

You can also add these entry points manually in the host application's SES Routing file: *config/Routes.cfm*. Why? Well, remember that routing is all about order, we must define our routes in order so they can be discovered. Thus, we need to manually go into our host application's Routes.cfm template and insert the module routes where we see fit. We do this by using a method called addModuleRoutes().

* addModuleRoutes(pattern, module) : Insert the module routes at this location in the configuration file with the applied URL pattern.

The URL pattern is the normal URL Mappings pattern we are used to and the module is the name of the module you want to target. What this does is create an entry point pattern that will identify the module's routing capabilities. For example, we create the following:

```js
addModuleRoutes(pattern="/blog",module="simpleblog");
addModuleRoutes(pattern="/admin",module="admin");
```

What the previous method calls do is bind a static URL entry pattern to a module. So if the framework detects an incoming URL with the starting point to be /blog, it will then match the simpleblog routes. Once matched, it will now try to match the rest of the incoming URL with the module's custom routes. Let's do a full example, below are some custom routes for my blog module in its *ModuleConfig.cfc*: