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

