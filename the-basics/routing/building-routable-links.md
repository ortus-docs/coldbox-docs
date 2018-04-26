# Building Routable Links

In your views, layouts and handlers you can use the `buildLink` method provided by the request context object to build routable links in your application.

```javascript
buildLink(
    // Where to link to
    any to, 
    // Translate periods to slashes
    [boolean translate='true'], 
    // Force or un-force SSL, by default we keep the same protocol of the request
    [boolean ssl], 
    // Update the baseURL, usually used for testing
    [any baseURL=''], 
    // Append a query string to the URL, as convention name value-pairs
    [any queryString='']
)
```

Just pass in the routed URL or event and it will create the appropriate routed URL for you:

```javascript
<a href="#event.buildLink( 'home.about' )#">About</a>
<a href="#event.buildLink( 'user.edit.id.#user.getID()#' )#">Edit User</a>
```

### Building Named Route URLs

ColdBox 5 supports named routes as well.  When you register a route you can assign a name to it and then generate a URL to it by using the `route()` method.

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

Then we can create routing URLs to them easily with the `route()` method:

```markup
<!-- Named Route with no params -->
<a href="#event.route( 'usermanager' )#">Manage Users</a>

<!-- Named Route with struct params -->
<a href="#event.route( 'userprofile', { id = 3 } )#">View User</a>

<!-- Named Route with array params -->
<a href="#event.route( 'userprofile', [ 3 ] )#">View User</a>
```

