# Sending Requests Somewhere

Once a route matches, it has to send the request somewhere. This page covers the four ways to do that: an **event**, a **handler**, a **view**, or an inline **response**.

## To an Event

The `to()` method sends a matched route directly to an event:

```javascript
route( "/wiki/:pagename" )
    .to( "wiki.show" );

route( "/users/:id/profile" )
    .as( "userProfile" )
    .to( "users:profile.show" );
```

You can also skip `to()` entirely and pass the event as the second argument to `route()`:

```javascript
route( "/wiki:pagename", "wiki.page" );

route(
    pattern = "/users/:id/profile",
    target  = "users:profile.show",
    name    = "userprofile"
);
```

## To a Handler

If the action is coming from the URL itself, or you're building RESTful actions, use `toHandler()` instead of naming a full event:

```javascript
// Action comes via the URL
route( "/users/:action" )
    .toHandler( "users" );

// RESTful actions - split by HTTP verb
route( "/users/:id?" )
    .withAction( {
        GET    : "index",
        POST   : "save",
        PUT    : "update",
        DELETE : "remove"
    } )
    .toHandler( "users" );
```

`withHandler()` + `withAction()` + `end()` is the long-form equivalent of `toHandler()`, useful when you're building the target dynamically:

```javascript
route( "wiki/:pagename" )
    .as( "wikipage" )
    .withHandler( "wiki" )
    .withAction( "show" )
    .end();
```

## To a View

`toView()` skips handlers entirely and renders a view (optionally with a layout):

```javascript
route( "/contact-us" )
    .toView( "main/contact" );

route( "/contact-us" )
    .toView(
        view         = "main/contact",
        layout       = "marketing",
        noLayout     = false,
        viewModule   = "moduleName",
        layoutModule = "moduleName"
    );
```

## To an Inline Response

`toResponse()` — or an inline closure/lambda passed straight to `route()` — lets you handle the request without a handler or a view at all:

```javascript
// Shortcut: closure as the second argument to route()
route( "/echo", ( event, rc, prc ) => "hello ColdBox!" );

// Equivalent, using the terminator
route( "/users/:id" )
    .toResponse( ( event, rc, prc ) => {
        var oUser = getInstance( "UserService" ).get( rc.id ?: 0 );
        if ( oUser.isLoaded() ) {
            return oUser.getMemento();
        }
        event.setHTTPHeader( statusCode = 400, statusText = "Invalid User ID provided" );
        return { "error" : true, "messages" : "Invalid User ID Provided" };
    } );
```

Every response closure receives three arguments:

| Argument | Description                                                |
| -------- | ------------------------------------------------------------ |
| `event`  | The request context - the object you use to work with the request |
| `rc`     | `URL`/`FORM` variables merged together (untrusted input)     |
| `prc`    | The private collection - only settable from inside your app  |

You can also pass a plain string. `{rc_var}` tokens inside it are replaced from the request collection:

```javascript
route( "/users/:id" )
    .toResponse( "<h1>Welcome back user: {id}, how are you today!</h1>" );

// The shortcut form works with strings too
route( "/echo/:name", "<h1>Hello {name} how are you today!</h1>" );
```

{% hint style="success" %}
**Next:** most real apps need more than one URL that redirects instead of responding - see [Redirecting Routes](redirects.md).
{% endhint %}
