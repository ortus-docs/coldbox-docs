---
description: >-
  Stream a Server-Sent Events response from any handler with event.sse() -
  the full emitter API, interception points, and settings. BoxLang only.
---

# Server-Sent Events

{% hint style="warning" %}
Server-Sent Events require **BoxLang**. `SSE()` is a core BoxLang BIF with no CFML equivalent.
{% endhint %}

This page covers the full `event.sse()` API - useful when a handler streams conditionally, or you want more control than a route-level terminator gives you.

## Starting a Stream

```javascript
function ticker( event, rc, prc ){
    event.sse( ( emitter ) => {
        while ( emitter.isOpen() ) {
            emitter.send( { "ts" : now() }, "tick" );
            sleep( 1000 );
        }
    } );
}
```

`sse()` takes over the response: ColdBox rendering is suppressed, any event-cache entry is discarded, and the flash scope is not auto-saved.

| Argument            | Description                                                    |
| -------------------- | ------------------------------------------------------------- |
| `callback`            | Required. A closure/lambda receiving the `emitter`             |
| `keepAliveInterval`   | Milliseconds between automatic keep-alive comments. `0` disables. Defaults to the `sse` setting below |
| `retry`               | Client reconnect hint in milliseconds. `0` omits the field     |
| `cors`                | CORS origin. `*` for all, empty for none                       |
| `headers`             | Additional response headers to set before the stream opens     |

Guard a conditional stream with `event.wantsSSE()` (true when `rc.format` resolved to `sse`, via an `Accept: text/event-stream` header or a `.sse` URL extension) or `event.isSSESupported()` if the same action needs to degrade gracefully on a non-BoxLang engine.

## The Emitter

| Method                                          | Description                                                              |
| ------------------------------------------------- | --------------------------------------------------------------------- |
| `isOpen()`                                        | `false` once the client disconnects - use it to end a streaming loop     |
| `send( data, event="", id="" )`                   | Send one frame. `data` can be simple or complex (auto-serialized)        |
| `sendData( data, type="json", event="", id="" )`   | Marshall `data` through the ColdBox DataMarshaller (`json`, `xml`, `wddx`, `plain`, `text`, `html`) instead of the default JSON |
| `sendView( view, args={}, layout="", module="", event="", id="" )` | Render a view (optionally with a layout) and push the markup as a frame |
| `sendLayout( layout, view="", args={}, module="", event="", id="" )` | Render a layout and push the markup as a frame               |
| `sendError( message, code="" )`                   | Send a conventional `event: error` frame carrying `{ error, code }`      |
| `sendIf( condition, data, event="", id="" )`       | Sugar for `if ( condition ) { emitter.send( data ) }`                    |
| `comment( text )`                                 | Send a raw SSE comment line                                              |
| `heartbeat()`                                     | Shortcut for `comment( "keep-alive" )`                                   |
| `close()`                                         | Gracefully end the stream. Safe to call more than once                   |

Every send method silently no-ops once the client has disconnected, so a streaming loop doesn't need to wrap every call in an `isOpen()` check - just use `isOpen()` to decide when to stop looping.

## Interception Points

Three interception points fire around a stream's lifecycle:

* **`preSSEConnection`** - fires before the stream opens. Set `data.abort = true` (and optionally `data.statusCode`) to reject the connection - the callback never runs, and the request is answered with a normal (non-streamed) response, so `abort` still has the full render pipeline available.
* **`postSSEConnection`** - fires after the stream closes normally. `data` carries `sentCount` and `duration` (milliseconds).
* **`onSSEError`** - fires if the callback throws. `data` carries `exception` and `sentCount`. The stream is closed on a best-effort basis and the exception is rethrown after the announce.

```javascript
function preSSEConnection( event, interceptData, rc, prc, buffer, data ){
    if ( !auth.isLoggedIn() ) {
        data.abort      = true;
        data.statusCode = 401;
    }
}
```

## Settings

Stream-wide defaults live in the `sse` settings block and can be overridden per-call on `event.sse()`:

```javascript
this.sse = {
    "keepAliveInterval" : 30000, // ms between automatic keep-alives - most proxies idle out at 60s
    "retry"              : 0,     // client reconnect hint in ms, 0 omits the field
    "cors"                : "*"    // CORS origin
};
```

## Guarding Against Committed Streams

`event.isSSE()` reports whether the current request has been taken over by a stream. `RestHandler.aroundHandler()` checks it (alongside `event.isNoExecution()` - see [HTTP Caching](../../digging-deeper/http-caching.md#writing-your-own-guard)) before writing its own response, to avoid a write-after-commit against a stream whose headers already went out. If you're writing your own `aroundHandler` or global `postProcess` interceptor, check `isSSE()` the same way before rendering unconditionally.

## Testing

Under a `MockController`, there's no live HTTP response to stream into - `event.sse()` detects this and substitutes a `MockSSEEmitter`, running the callback synchronously against it. Handler code is identical either way, so no special-casing is needed in the handler itself; assert on what was sent via the test harness's SSE matchers.
