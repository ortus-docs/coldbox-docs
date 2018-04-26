# Event Routing

Apart from routing by convention, you can also register your own expressive routes.  Let's investigate the routing approaches.

### Inline Terminator

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

#### Inline Response

You can also pass in a closure or lambda to the target argument and it will be treated as an inline action:

```java
route(
    pattern="/echo",
    response=function( event, rc, prc ){
        return "hello ColdBox!";
    }
);

route(
    pattern="/users",
    response=function( event, rc, prc ){
        return getInstance( "UserService" ).list();
    }
);
```

To read more about responses please see the [Route Responses](route-responses.md) section.

### Routing `to` Events

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

### Routing To Handler Action Combinations

You can also route to a **handler** and an **action** using the modifiers instead of the `to()` method. This long-form is usually done for visibility or dynamic writing of routes.  You can use the following methods:

* `withHandler()`
* `withAction()`
* `toHandler()`
* `end()`

```java
route( "wiki/:pagename" )
    as( "wikipage" )
    withAction( "show" )
    toHandler( "wiki" );
    
route( "wiki/:pagename" )
    .withHander( "wiki" )
    .withAction( "show" )
    .end();
```

### Routing to Views

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

### Routing to Redirects

You can also use the `toRedirect()` method to re-route patterns to other patterns. 

```java
route( "/my-old/link" )
    .toRedirect( target="/new/pattern", statusCode=301 );
```

{% hint style="info" %}
The default status code for redirects are 301 redirects which are PERMANENT redirects.
{% endhint %}

### Routing to Handlers

You can also redirect a pattern to a handler using the `toHandler()` method.  This is usually done if you have the action coming in via the URL or you are using RESTFul actions.

```java
// Action comes via the URL
route( "/users/:action" )
    .toHandler( "users" );
```

### Routing to RESTFul Actions

You can also route a pattern to RESTFul actions.  This means that you can split the routing pattern according to incoming HTTP Verb.  You will use a modifier `withAction() `and then assign it to a handler via the `toHandler()` method.

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

