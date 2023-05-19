# Linking Events Together

ColdBox provides you with a nice method for generating links between events by leveraging an object called `event` that is accessible in all of your layouts/views and event handlers. This `event` object is called behind the scenes the [**request context object**](../../the-basics/request-context.md)**,** which models the incoming request and even contains all of your incoming `FORM` and `URL` variables in a structure called `rc`.

{% hint style="success" %}
**Tip**: You will use the event object to set views, set layouts, set HTTP headers, read HTTP headers, convert data to other types (json,xml,pdf), and much more.
{% endhint %}

## Building Links

You can easily build links with ColdBox by using two methods:

1. `event.buildLink()` - Build links to events or URL routes
2. `event.route()` - Build links to specifically [named routes](../../the-basics/routing/routing-dsl/named-routes.md)

Here are the signatures

```java
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
)


/**
 * Builds links to named routes with or without parameters. If the named route is not found, this method will throw an `InvalidArgumentException`.
 * If you need a route from a module then append the module address: `@moduleName` or prefix it like in run event calls `moduleName:routeName` in order to find the right route.
 *
 * @name   The name of the route
 * @params The parameters of the route to replace
 * @ssl    Turn SSL on/off or detect it by default
 *
 * @throws InvalidArgumentException - If thre requested route name is not registered
 */
string function route( required name, struct params = {}, boolean ssl )

```

## Edit Your View

Edit the `views/virtual/hello.cfm` page and wrap the content in a `cfoutput` and create a link to the main ColdBox event, which by convention, is `main.index`.  You can use `main.index` or just `main` (Remember that `index` is the default action)

```markup
<cfoutput>
    <h1>Hello from ColdBox Land!</h1>
    <p><a href="#event.buildLink( "main" )#">Go home</a></p>
</cfoutput>
```

This code will generate a link to the `main.index` event in a search engine-safe manner and in SSL detection mode. Go execute the event: `http://localhost:{port}/virtual/hello` and click on the generated URL; you will now be navigating to the default event `/main/index`. This technique will also apply to FORM submissions:

```markup
<form action="#event.buildLink( 'user.save' )#" method="post">
...
</form>
```

{% hint style="success" %}
**Tip** You can visit our API Docs for further information about the `event` object and the `buildLink` method: [http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html](http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html).

For extra credit try to use more of the `buildLink` arguments.
{% endhint %}

## Routing

We have been using routing by convention, but let's do named routes now to control the URL.  Let's create a `/home` route that will execute the `main.index` event and update our view to change the building of the URL via `route()`. Let's open the `config/Router.cfc`

```cfscript
// @app_routes@

route( "/home" ).as( "home" ).to( "main.index" );

// Conventions-Based Routing
route( ":handler/:action?" ).end();
```

We use the `route()` method to register URL patterns and then tell the router what to execute if matched.  This can be an event, but it can also be a view, an inline action, a relocation, and much more. Since we registered new URLs you need to reinit the app (`?fwreinit=1`).  Now let's update the link in the `hello` view:

```html
<cfoutput>
    <h1>Hello from ColdBox Land!</h1>
    <p><a href="#event.route( "home" )#">Go home</a></p>
</cfoutput>
```

Try it out now!

{% hint style="success" %}
**Tip:** Check out the routing API Docs for further information.
{% endhint %}
