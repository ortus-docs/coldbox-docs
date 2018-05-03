# Building Routable Links

In your views, layouts and handlers you can use the `buildLink` method provided by the request context object \(**event**\) to build routable links in your application.

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

### **Inspecting The Current Route**

The request context object \(**event**\) also has some handy methods to tell you the name or even the current route that was selected for execution:

* `getCurrentRouteName()` - Gives you the name of the current route, if any
* `getCurrentRoute()` - Gives you the currently executed route
* `getCurrentRoutedURL()` - Gives you the complete routed URL pattern that matched the route
* `getCurrentRoutedNamespace()` - Gives you the current routed namespace, if any



