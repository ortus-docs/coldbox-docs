# Redirecting Routes

Use `toRedirect()` to send one pattern to another location instead of executing an event.

```javascript
route( "/my-old/link" )
    .toRedirect( target = "/new/pattern", statusCode = 301 );
```

{% hint style="info" %}
The default status code is **301** - a permanent redirect. Pass `statusCode` for a temporary (`302`) redirect instead.
{% endhint %}

## Dynamic Redirects

`target` can also be a closure/lambda instead of a string. It receives the matched **route**, the parsed **params**, and the **event**, and must return the destination:

```javascript
route( "/my-old/link" )
    .toRedirect( ( route, params, event ) => "/new/route" );
```

This is useful when the destination depends on what was actually matched:

```javascript
route( "/old/api/users/:id" )
    .toRedirect( ( route, params, event ) => {
        return "/api/v1/users/#params.id#";
    } );
```
