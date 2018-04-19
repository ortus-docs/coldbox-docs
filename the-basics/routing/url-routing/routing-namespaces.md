# Routing Namespaces

You can create a-la-carte namespaces for URL routes. Namespaces are cool groupings of routes according to a specific URL entry point. So you can say that all URLs that start with /testing will be found in the testing namespace and it will iterate through the namespace routes until it matches one of them. Much how modules work, where you have a module entry point, now you can create virtual entry point to ANY route by namespacing it. This route can be a module a non-module, package, or whatever you like. You start off by registering the namespace using the `addNamespace(pattern, namespace)` method:

```javascript
addNamespace(pattern="/testing", namespace="test");
addNamespace(pattern="/news", namespace="blog");
```

Once a namespace is registered you can add routes to it via the `addRoute()` method or the `with()` closure.

```javascript
// Via addRoute
addNamespace(pattern="/news", namespace="blog")
  .addRoute(pattern="/",handler="blog",action="index",namespace="blog")
  .addRoute(pattern="/:year-numeric?/:month-numeric?/:day-numeric?",handler="blog",action="index",namespace="blog");

// Via with closure
addNamespace(pattern="/news", namespace="blog");
with(namespace="blog", handler="blog")
  .addRoute(pattern="/",action="index")
  .addRoute(pattern="/:year-numeric?/:month-numeric?/:day-numeric?",action="index");
.endWith();
```

> **Hint** You can also register multiple URL patterns that point to the same namespace

