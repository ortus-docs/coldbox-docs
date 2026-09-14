---
description: >-
  Terminate a route with a Server-Sent Events stream instead of a normal
  response. BoxLang only.
---

# Streaming Routes (SSE)

{% hint style="warning" %}
🚀 **BoxLang Exclusive** — `toSSE()` requires **BoxLang**. It is not available on CFML engines.
{% endhint %}

`toSSE()` terminates a route by handing it over to a [Server-Sent Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events) stream instead of a normal request/response cycle.

```javascript
route( "/events/heartbeat" ).toSSE( ( event, rc, prc, emitter ) => {
    while ( emitter.isOpen() ) {
        emitter.send( { "ts" : now() }, "heartbeat" );
        sleep( 5000 );
    }
} );
```

The callback receives the usual `event`, `rc`, `prc` - plus an **`emitter`**:

| Method                        | Description                                          |
| ------------------------------ | ----------------------------------------------------- |
| `emitter.isOpen()`              | `false` once the client disconnects - use it to end your loop |
| `emitter.send( data, event, id )` | Send one SSE frame. `data` can be simple or complex (auto-serialized) |
| `emitter.comment( text )`        | Send a raw comment line - useful for manual keep-alives |
| `emitter.close()`                | Gracefully end the stream                            |

Once `toSSE()` takes over, ColdBox rendering is suppressed for that request, any event-cache entry is discarded, and the flash scope is **not** auto-saved - a stream can stay open for minutes, so persisting flash values on it would leak into an unrelated later request.

## When You Have Both a JSON and a Streaming Representation

Use `toSSE()` only for endpoints that *always* stream. If a resource sometimes streams and sometimes responds normally, leave the route pointing at a handler and branch inside the action instead:

```javascript
route( "/reports/:id" ).toHandler( "reports" );
```

```javascript
// handlers/Reports.cfc
function show( event, rc, prc ){
    if ( event.wantsSSE() ) {
        return event.sse( ( emitter ) => {
            // ... stream it
        } );
    }
    // ... normal response
}
```

## Configuring Defaults

Stream-wide defaults (keep-alive interval, reconnect hint, CORS) come from the `sse` settings block and can be overridden per-call on `event.sse()`. See the request context's `sse()` method for the full set of options.
