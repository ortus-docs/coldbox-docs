# Asynchronous Annotations

We have also extended the interceptor registration process so you can annotate interception points to denote threading. You will do so with the following two annotations:

| Argument | Type | Required | Default Value | Description |
| --- | --- | --- | --- | --- |
| `async` | none | false | false | If the annotation exists, then ColdBox will execute the interception point in a separate thread only if not in a thread already. |
| `asyncPriority` | string : _low,normal,high_ | false | _normal_ | The thread priority that will be sent to each cfthread call that is made by the system. |

This allows you the flexibility to determine in code which points are threaded, which is a great way to use for emails, logging, etc.

```javascript
function preProcess( event, interceptData ) async asyncPriority="low"{
    // Log current request information
    log.info("Executing request: #event.getCurrentEvent()#", getHTTPRequestData() );    
}
```

So if you have 3 interceptors in a chain and only 1 of them is threaded, then the 2 will execute in order while the third one in the background. Overall, your processing power and choices have now grown.

