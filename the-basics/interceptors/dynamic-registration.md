---
description: Register one-off closure listeners on interception points directly in code using the interceptor service listen() method.
---

# Dynamic Registration

Instead of creating an Interceptor and registering this interceptor programmatically or in configuration, you can do this directly in code by registrering one-off closures with the new `listen` supertype.

```javascript
controller.getInterceptorService().listen( () => {
    log.info( "executing from closure listener")
}, "preProcess" );
```
