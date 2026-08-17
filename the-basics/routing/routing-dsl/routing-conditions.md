# Routing Conditions

`withCondition()` attaches a runtime check to a route. Even if the URL pattern matches, the route only fires if the closure returns `true`.

```javascript
route( "/go/firefox" )
    .withCondition( function( requestString ){
        return findNoCase( "Firefox", cgi.HTTP_USER_AGENT ) ? true : false;
    } )
    .to( "firefox.index" );
```

This is useful for things like browser sniffing, feature flags, or A/B routing - any case where the URL alone isn't enough to decide whether a route should apply.

{% hint style="info" %}
A condition only decides **whether** the route matches. It doesn't stop or shape the request the way [route middleware](middleware.md) does - if you need to reject, redirect, or transform the request once matched, middleware is the right tool.
{% endhint %}
