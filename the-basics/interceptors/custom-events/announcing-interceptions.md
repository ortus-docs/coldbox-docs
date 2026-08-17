# Announcing Interceptions

The last piece of the puzzle is how to announce events. You will do so via the inherited super type method `announce()` that all your handlers,plugins and even the interceptors themselves have or via the interceptor service `announce()` method. This method accepts an incoming data struct which will be broadcasted alongside your event:

```javascript
// Announce with no data
announce( "onExit" );

// Announce with data
announce( 'onLog', {
    time = now(),
    user = event.getValue( "user" ),
    dataset = prc.dataSet
} );

// Announce via interceptor service
controller.getInterceptorService().announce( "onRecordInsert", {} );
```

> **Hint** Announcing events can also get some asynchronous love, read the [Interceptor Asynchronicity](../interceptor-asynchronicity/) for some asynchronous love.

## Detecting a Short-Circuit

On the default synchronous path, `announce()` returns `true` if an interceptor short-circuited the chain by returning `true` from its handler, `false` otherwise (interceptors that never return a boolean, and points with no registered interceptors, resolve to `false`). Use this to detect that an interceptor consumed or rejected the announcement:

```javascript
if ( controller.getInterceptorService().announce( "preSSEConnection", data ) ) {
    // an interceptor returned true - the chain was short-circuited
}
```

{% hint style="info" %}
This only applies to the synchronous path. `async`/`asyncAll` announcements return a thread structure report instead, as documented in [Interceptor Asynchronicity](../interceptor-asynchronicity/).
{% endhint %}
