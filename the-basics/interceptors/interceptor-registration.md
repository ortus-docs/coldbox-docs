# Interceptor Registration

You can also register CFCs as interceptors programmatically by talking to the application's Interceptor Service that lives inside the main ColdBox controller. You can access this service like so:

```javascript
// via the controller
controller.getInterceptorService();

// dependency injection
property name="interceptorService" inject="coldbox:interceptorService";
```

Once you have a handle on the interceptor service you can use the following methods to register interceptors:

* `registerInterceptor()` - Register an instance or CFC path and discover all events it listens to by conventions
* `registerInterceptionPoint()` - Register an instance in a specific event only

Here are the method signatures:

```javascript
public any registerInterceptor([any interceptorClass], [any interceptorObject], [any<struct> interceptorProperties='[runtime expression]'], [any customPoints=''], [any interceptorName])

public any registerInterceptionPoint(any interceptorKey, any state, any oInterceptor)
```

**Examples**

```javascript
// register yourself to listen to all events declared
controller.getInterceptorService()
    .registerInterceptor( interceptorObject=this );

// register yourself to listen to all events declared and register new events: onError, onLogin
controller.getInterceptorService()
    .registerInterceptor( interceptorObject=this, customPoints="onError,onLogin" );

// Register yourself to listen to the onException event ONLY
controller.getInterceptorService()
    .registerInterceptionPoint( 
        interceptorKey="MyService", 
        state="onException", 
        oInterceptor=this
     );
```

