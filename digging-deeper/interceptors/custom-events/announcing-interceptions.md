# Announcing Interceptions

The last piece of the puzzle is how to announce events. You will do so via the inherited super type method `announceInterception()` that all your handlers,plugins and even the interceptors themselves have or via the interceptor service `processState()` method. This method accepts an incoming data struct which will be broadcasted alongside your event:

```javascript
// Announce with no data
announceInterception( "onExit" );

// Announce with data
announceInterception( 'onLog', {
    time = now(),
    user = event.getValue( "user" ),
    dataset = prc.dataSet
} );

// Announce via interceptor service
controller.getInterceptorService().processState( "onRecordInsert", {} );
```

> **Hint** Announcing events can also get some asynchronous love, read the [Interceptor Asynchronicity](../interceptor-asynchronicity/) for some asynchronous love.

