# Building Routable Links

In your views, layouts and handlers you can use the `buildLink` method provided by the request context object (**event**) to build routable links in your application.

```javascript
/**
 * Builds links to events or URL Routes
 *
 * @to          The event or route path you want to create the link to
 * @queryString The query string to append which can be a regular query string string, or a struct of name-value pairs
 * @translate   Translate between . to / depending on the SES mode on to and queryString arguments. Defaults to true.
 * @ssl         Turn SSl on/off on URL creation, by default is SSL is enabled, we will use it.
 * @baseURL     If not using SES, you can use this argument to create your own base url apart from the default of index.cfm. Example: https://mysample.com/index.cfm
 */
string function buildLink(
	to,
	queryString       = "",
	boolean translate = true,
	boolean ssl,
	baseURL = ""
){
```

Just pass in the routed URL or event and it will create the appropriate routed URL for you:

```javascript
<a href="#event.buildLink( 'home.about' )#">About</a>
<a href="#event.buildLink( 'user.edit.id.#user.getID()#' )#">Edit User</a>
```

### QueryString Struct

The `queryString` argument can be a simple query string or a struct that represents the query variables to append.

```html
<a href="#event.buildLink( 'home.about', "page=2&format=simple" )#">About</a>

<a href="#event.buildLink( 'home.about', { page : 2, format: "simple" } )#">About</a>

```

### Named Routes

Please note that the `to` argument can be a simple route path, but it can also be a struct.  This struct is for routing to named routes. Even though we recommend to use the `route()` method instead.

```javascript
event.buildLink( {
    name : "routeName",
    params : { ... }
} )

event.route( "routeName", { ... } )
```

### **Inspecting The Current Route**

The request context object (**event**) also has some handy methods to tell you the name or even the current route that was selected for execution:

* `getCurrentRouteName()` - Gives you the name of the current route, if any
* `getCurrentRoute()` - Gives you the currently executed route
* `getCurrentRoutedURL()` - Gives you the complete routed URL pattern that matched the route
* `getCurrentRoutedNamespace()` - Gives you the current routed namespace, if any

