# Named Routes

You can register routes in ColdBox with a human friendly name so you can reference them later for link generation and more.  

### Registering Named Routes

You will do this in two forms:

1. Using the` route()` method and the `name` argument
2. Using the `as() `method

```java
// Using the name argument
route( 
    pattern = "/users/list", 
    target = "users.index", 
    name = "usermanager" 
);

route( 
    pattern = "/user/:id/profile", 
    target = "users.show", 
    name = "userprofile"
);

// Using the as() method
route( "/users/:id/profile" )
  .as( "usersprofile" )
  .to( "users.show" )
```

### Generating URLs to Named Routes

You will generate URLs to named routes by leveraging the `route()` method in the request context object \(**event**\).

```javascript
route(
    // The name of the route
    required name,
    // The params to pass, can be a struct or array
    struct params={},
    // Force or un-force SSL, by default we keep the same protocol of the request
    boolean ssl
);
```

Let's say you register the following named routes:

```java
route( 
    pattern = "/users/list", 
    target = "users.index", 
    name = "usermanager" 
);

route( 
    pattern = "/user/:id/profile", 
    target = "users.show", 
    name = "userprofile"
);
```

Then we can create routing URLs to them easily with the `event.route()` method:

```markup
<!-- Named Route with no params -->
<a href="#event.route( 'usermanager' )#">Manage Users</a>

<!-- Named Route with struct params -->
<a href="#event.route( 'userprofile', { id = 3 } )#">View User</a>

<!-- Named Route with array params -->
<a href="#event.route( 'userprofile', [ 3 ] )#">View User</a>
```

### **Inspecting The Current Route**

The request context object \(**event**\) also has some handy methods to tell you the name or even the current route that was selected for execution:

* `getCurrentRouteName()` - Gives you the name of the current route, if any
* `getCurrentRoute()` - Gives you the currently executed route
* `getCurrentRoutedURL()` - Gives you the complete routed URL pattern that matched the route
* `getCurrentRoutedNamespace()` - Gives you the current routed namespace, if any

