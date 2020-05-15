# Default Route Execution

By now, we should all know the default SES route ColdBox offers: `addRoute(":handler/:action?")`. This means that we can target a handler with or without a package and an action for execution. In ColdBox we have also added a package resolver that will detect this pattern for module and package directories so we can do URL safe and SES friendly URLs for these executions by convention.

If we do not have this feature, this is how the URLs would look like:

```javascript
http://mysite.com/module:handler/action
or
http://mysite.com/module:package.handler/action
```

However, thanks to package resolving in the SES interceptor we can do links like this:

```javascript
http://mysite.com/module/handler/action
or
http://mysite.com/module/package/handler/action
```

Much better and nicer huh? Of course! So potentially, with one route we could write entire applications.

