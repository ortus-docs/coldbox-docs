# Interceptors

All interceptor events receive the following arguments:

* `event` - The request context object
* `interceptData` - The interception data
* `buffer` - The request buffer

They do not receive the two references, so if you need them in your events, you will need to reference them manually:

```javascript
function preProcess(event, interceptData){
    var rc = event.getCollection();
    var prc = event.getPrivateCollection();    
}
```

