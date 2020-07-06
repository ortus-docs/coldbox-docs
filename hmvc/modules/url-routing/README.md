# URL Routing

All modules have the capability to leverage URL routing in a portable manner. They will automatically register an entry point URL pattern via the `this.entryPoint` setting. If an incoming URL has that specific pattern, then it will search for the module's routing table for a match.

```javascript
this.entryPoint = "/mymodule";
```

## Parent Manual Routing

You can also add these entry points manually in the host application's routing file: `config/Router.cfc`. However, you will lose all module portability. We do this by using a method called `addModuleRoutes()` method.

* `addModuleRoutes(pattern, module)` : Insert the module routes at this location in the configuration file with the applied URL pattern.

The URL pattern is the normal URL Mappings pattern we are used to and the module is the name of the module you want to target. What this does is create an entry point pattern that will identify the module's routing capabilities. For example, we create the following:

```javascript
addModuleRoutes(pattern="/blog",module="simpleblog");
addModuleRoutes(pattern="/admin",module="admin");
```

What the previous method calls do is bind a static URL entry pattern to a module. So if the framework detects an incoming URL with the starting point to be /blog, it will then match the simpleblog routes. Once matched, it will now try to match the rest of the incoming URL with the module's custom routes. Let's do a full example, below are some custom routes for my blog module in its `ModuleConfig.cfc`:

```javascript
// In my ModuleConfig.cfc
routes = [
    {pattern="/", handler="blog", action="showPosts"},
    {pattern="/:year-numeric/:month-numeric?", handler="blog", action="showPosts"}
    {pattern="/comment/:action", handler="comments"}
]
```

Now, let's say the URL that is incoming is:

```javascript
http://mysite.com/blog
```

Then this will resolve to the `/blog` pattern in the host that says, now look in the module simpleblog for routes. The left over part of the URL is nothing or `/` so the pattern that matches is my first declared pattern:

```javascript
{pattern="/", handler="blog", action="showPosts"}
```

This means, that we will execute the modules' `blog` handler and the `showPosts` method. Now, let's say the next URL that comes is:

```javascript
http://mysite.com/blog/2009/09
```

Then this will match the `simpleblog` module via the static `/blog` entry point and then it tries to find a match for `/2009/09` in the modules' routes and it does!

