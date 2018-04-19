# Unregistering Interceptors

Each interceptor can unregister itself from any event by using the `unregister( state )` method. This method is found in all Interceptors or can be accessed via the interceptor service as well.

```javascript
// Inside an interceptor
unregister( 'preProcess' );

// From the interceptor service
controller.getInterceptorService()
    .unregister( interceptorName="MyInterceptor", state="preProcess" );
```

