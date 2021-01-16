# Routing Methods

Apart from routing by convention, you can also register your own expressive routes. Let's investigate the routing approaches.

## Inline Terminator

The `route()` method allows you to register a **pattern** and immediately assign it to execute an event or a response via the **target** argument.

```java
route( "/wiki:pagename", "wiki.page" );
route( 
    pattern="/users/:id/profile", 
    target="users:profile.show", 
    name="userprofile" 
);
```

The first pattern registers and if matched it will execute the **wiki.page** event. The second pattern if matched it will execute the **profile.show** event from the **users** module and register the route with the **userprofile** name.

### Inline Responses

You can also pass in a closure or lambda to the target argument and it will be treated as an inline action:

```java
route(
    pattern="/echo",
    target=function( event, rc, prc ){
        return "hello ColdBox!";
    }
);

route(
    pattern="/users",
    target=function( event, rc, prc ){
        return getInstance( "UserService" ).list();
    }
);
```

You can also pass just an HTML string with `{rc_var}` replacements for the routed variables place in the request collection

```javascript
route(
    "/echo/:name",
    "<h1>Hello {name} how are you today!</h1>"
);
```

## Routing `to` Events

If you will not use the inline terminators you can do a full expressive route definition to events using the `to()` method, which allows you to concatenate the route pattern with modifiers:

```java
route( "/wiki/:pagename" )
    .to( "wiki.show" );

route( "/users/:id/profile" )
    .as( "userProfile" )
    .to( "users:profile.show" );

route( "/users/:id/profile" )
    .as( "userProfile" )
    .withVerbs( "GET" )
    .withSSL()
    .header( "cache", false )
    .prc( "isActive", true )
    .to( "users:profile.show" );
```

## Routing To Handler Action Combinations

You can also route to a **handler** and an **action** using the modifiers instead of the `to()` method. This long-form is usually done for visibility or dynamic writing of routes. You can use the following methods:

* `withHandler()`
* `withAction()`
* `toHandler()`
* `end()`

```java
route( "wiki/:pagename" )
    .as( "wikipage" )
    .withAction( "show" )
    .toHandler( "wiki" );

route( "wiki/:pagename" )
    .withHander( "wiki" )
    .withAction( "show" )
    .end();
```

## Routing to Views

You can also route to views and view/layout combinations by using the `toView()` terminator:

```java
route( "/contact-us" )
    .toView( 
        view = "view name",
        layout = "layout",
        nolayout = false,
        viewModule = "moduleName",
        layoutModule = "moduleName"
    );
```

## Routing to Redirects

You can also use the `toRedirect()` method to re-route patterns to other patterns.

```java
route( "/my-old/link" )
    .toRedirect( target="/new/pattern", statusCode=301 );
```

{% hint style="info" %}
The default status code for redirects are 301 redirects which are PERMANENT redirects.
{% endhint %}

## Routing to Handlers

You can also redirect a pattern to a handler using the `toHandler()` method. This is usually done if you have the action coming in via the URL or you are using RESTFul actions.

```java
// Action comes via the URL
route( "/users/:action" )
    .toHandler( "users" );
```

## Routing to RESTFul Actions

You can also route a pattern to HTTP RESTFul actions. This means that you can split the routing pattern according to incoming HTTP Verb. You will use a modifier `withAction()` and then assign it to a handler via the `toHandler()` method.

```java
// RESTFul actions
route( "/users/:id?" )
    .withAction( {
        GET : "index",
        POST : "save",
        PUT : "update",
        DELETE : "remove"
    } )
    .toHandler( "users" );
```

## Routing to Responses

The Router allows you to create inline responses via closures/lambdas or enhanced strings to incoming URL patterns. You do not need to create handler/actions, you can put the actions inline as responses.

```javascript
/**
 * Setup a response for a URL pattern
 * @body A closure/lambda or enhanced HTML string
 * @statusCode The status code to send
 * @statusText The status code to send
 */
function toResponse( 
    required body, 
    numeric statusCode = 200, 
    statusText = "Ok" 
)
```

If you use a response closure/lambda, they each accepts three arguments:

1. `event` - An object that models and is used to work with the current request \(Request Context\)
2. `rc` - A struct that contains both `URL/FORM` variables merged together \(unsafe data\)
3. `prc` - A secondary struct that is **private** only settable from within your application \(safe data\)

```java
// Simple response routing
route( "/users/hello", function( event, rc, prc ){
    return "<h1>Hello From RESTLand</h1>";
} );

// Simple response routing with placeholders
route( "/users/:username", function( event, rc, prc ){
    return "<h1>Hello #encodeForHTML( rc.username )# From RESTLand</h1>";
} );

// Routing with the toResponse() method
route( "/users/:id" )
    .toResponse( function( event, rc, prc ){
        var oUser = getInstance( "UserService" ).get( rc.id ?: 0 );
        if( oUser.isLoaded() ){
            return oUser.getMemento();
        }
        event.setHTTPHeader( statusCode = 400, statusText = "Invalid User ID provided" );
        return {
            "error" : true,
            "messages" : "Invalid User ID Provided"
        };
    } );
```

If the response is an HTML string, then you can do `{rc_var}` replacements on the strings as well:

```javascript
// Routing with enhanced HTML strings
route( "/users/:id" )
    .toResponse(
        "<h1>Welcome back user: {id} how are you today!</h1>"
    );
```

## Sub-Domain Routing

You can also register routes that will respond to sub-domains and even capture portions of the sub-domain for multi-tenant applications or SaaS applications. You will do this using the `withDomain()` method.

```javascript
route( "/" )
  .withDomain( "subdomain-routing.dev" )
  .to( "subdomain.index" );

route( "/" )
  .withDomain( ":username.forgebox.dev" )
  .to( "subdomain.show" );
```

You can leverage the full routing DSL as long as you add the `withDomain()` call with the domain you want to bind the route to. Also note that the domain string can contain **placeholders** which will be translated to `RC` variables for you if matched.

{% hint style="success" %}
**Tip:** Please note that you can leverage [Routing Groups](routing-groups.md) as well for domains
{% endhint %}

## Adding Variables to RC/PRC

You can also add variables to the RC and PRC structs on a per-route basis by leveraging the following methods:

* `rc( name, value, overwrite=true )` - Add an `RC` value if the route matched
* `rcAppend map, overwrite=true )` - Add multiple values to the `RC` collection if the route matched
* `prc( name, value, overwrite=true )` - Add an `PRC` value if the route matched
* `prcAppend map, overwrite=true )` - Add multiple values to the `PRC` collection if the route matched

This is a great way to manually set variables in the incoming structures:

```java
route( "/api/v1/users/:id" )
    .rcAppend( { secured : true } )
    .prcAppend( { name : "hello" } )
    .to( "api-v1:users.show" );
```

## Routing Conditions

You can also apply runtime conditions to a route in order for it to be matched. This means that if the route matches the URL pattern then we will execute a closure/lambda to make sure that it meets the runtime conditions. We will do this with the `withCondition(`\) method.

Let's say you only want to fire some routes if they are using Firefox, or a user is logged in, or whatever.

```javascript
route( "/go/firefox" )
  withCondition( function( requestString ){
    return ( findnocase( "Firefox", cgi.HTTP_USER_AGENT ) ? true : false );
  });
  .to( "firefox.index" );
```

