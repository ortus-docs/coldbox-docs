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

```js
// In my ModuleConfig.cfc
routes = [
  {pattern="/", handler="blog", action="showPosts"},
    {pattern="/:year-numeric/:month-numeric?", handler="blog", action="showPosts"}
    {pattern="/comment/:action", handler="comments"}
]
```

Now, let's say the URL that is incoming is:

```js
http://mysite.com/blog
```

Then this will resolve to the */blog* pattern in the host that says, now look in the module simpleblog for routes. The left over part of the URL is nothing or / so the pattern that matches is my first declared pattern:

```js
{pattern="/", handler="blog", action="showPosts"}
```

This means, that we will execute the modules' blog handler and the showPosts method. Now, let's say the next URL that comes is:

```js
http://mysite.com/blog/2009/09
```

Then this will match the *simpleblog* module via the static /blog entry point and then it tries to find a match for */2009/09* in the modules' routes and it does! So in conclusion, to enable module SES or URL Mappings you must do two things:

1. Define your routes in the ModuleConfig configuration object
2. Add the entry point for the module routes wherever you see fit in the host application's Routes.cfm configuration file by using the addModuleRoutes() method.

