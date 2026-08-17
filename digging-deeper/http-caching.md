---
description: >-
  Conditional-GET (ETag/Last-Modified) and Cache-Control support, usable in
  any handler or piggybacked automatically on event caching.
---

# HTTP Caching

ColdBox gives you two ways to add standards-based HTTP caching headers to a response: call the primitives yourself from any handler, or opt an already-`cache="true"` event into computing them automatically.

## Manual Conditional-GET

`event.etag()` and `event.lastModified()` set the corresponding response header and check it against the incoming conditional request header. If they match, the request is short-circuited with a `304 Not Modified` and no body.

```javascript
function show( event, rc, prc ){
    prc.product = productService.get( rc.id );

    if ( event.etag( prc.product.getHash() ) ) {
        return; // 304 already sent - nothing left to do
    }

    event.setView( "products/show" );
}
```

| Method                                  | Sets                | Matches against       |
| ---------------------------------------- | -------------------- | ----------------------- |
| `event.etag( value, weak=false )`        | `ETag`                | `If-None-Match`          |
| `event.lastModified( value )`            | `Last-Modified`       | `If-Modified-Since`      |
| `event.cacheControl( directives )`       | `Cache-Control`       | -                        |

Both `etag()` and `lastModified()` return `true` when they short-circuited the request - check the return value and `return` early, same as the example above. Per [RFC 7232](https://www.rfc-editor.org/rfc/rfc7232), a request carrying `If-None-Match` has its `If-Modified-Since` ignored, so call `etag()` if you have both a tag and a timestamp available.

{% hint style="info" %}
Neither method ever short-circuits an unsafe HTTP method (anything but `GET`/`HEAD`) - a `304` in response to a `POST` would be a protocol violation, so mutating requests always proceed regardless of matching tags.
{% endhint %}

`cacheControl()` builds the header from a directives struct - boolean `true` becomes a bare directive, anything else becomes `key=value`:

```javascript
event.cacheControl( {
    "public"                 : true,
    "max-age"                : 60,
    "stale-while-revalidate" : 30
} );
// Cache-Control: public, max-age=60, stale-while-revalidate=30
```

## REST Responses

If you're building on the [RestHandler](rest-handler.md) and its `Response` object, `withETag()` and `withCacheControl()` are the fluent equivalents:

```javascript
function show( event, rc, prc ){
    event.getResponse()
        .withETag( productService.get( rc.id ).getHash() )
        .setData( prc.product.getMemento() );
}
```

They set the same headers as `event.etag()`/`event.cacheControl()` - use whichever fits the style of the handler you're in.

## Automatic Conditional-GET on Cached Events

If an action is already using [event caching](../the-basics/event-handlers/event-caching.md) (`cache="true"`), you can opt it into an automatically computed `ETag`/`Last-Modified` instead of calling the primitives yourself - see [event caching's HTTP caching annotations](../the-basics/event-handlers/event-caching.md#http-caching-annotations) for the full annotation reference. It's a good fit when the cached output itself is a fine proxy for "has this changed," and you don't need control over exactly what the tag represents.

## Writing Your Own Guard

`event.isNoExecution()` reports whether the current request was already short-circuited (by `etag()`, `lastModified()`, or an SSE takeover) - the same guard `RestHandler.aroundHandler()` checks before writing a response, to avoid a write-after-commit against a request a conditional-GET already resolved. If you're writing your own `aroundHandler` or a global `postProcess` interceptor that renders unconditionally, check it first:

```javascript
function postProcess( event, interceptData, rc, prc ){
    if ( event.isNoExecution() ) {
        return; // a 304 (or an SSE stream) already committed the response
    }
    // ... your logic
}
```
