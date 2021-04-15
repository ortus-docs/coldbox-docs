# Async Listeners With Join

The second use case is where you want to run the interception but multi-thread **all** the interceptor CFCs that are listening to that interception point. So let's say we have our `onPageCreate` announcement and we have 3 interceptors that are listening to that interception point. Then by using the `asyncAll=true` argument, ColdBox will create 3 separate threads, one for each of those interceptors and execute their methods in their appropriate threads. This is a great way to delegate long running processes simultaneously on a specific piece of data. Also, by default, the caller will wait for all of those 3 threads to finalize before continuing execution. So it is a great way to process data and wait until it has become available.

```javascript
var threadData = announce(
    state           = "onPageCreate", 
    data            = {}, 
    asyncAll        = true
);
```

> **Caution** Please remember that you are also sharing state between interceptors via the `event` and `interceptData`, so make sure you either lock or are careful in asynchronous land.

## Configuration Arguments

You can also combine this call with the following arguments:

* `asyncPriority` : The priority level of each of the spawned threads. By default it uses `normal` priority level 
* `asyncJoinTimeout` : The timeout in milliseconds to wait for all spawned threads. By default it is set to 0, which waits until ALL threads finalize no matter how long they take.
* `asyncAllJoin` : The flag that determines if the caller should wait for all spawned threads or should just spawn all threads and continue immediately. By default, it waits until all spawned threads finalize.

```javascript
var threadData = announce(state="onPageCreate", interceptData={}, asyncAll=true, asyncAllJoin=false);
var threadData = announce(
    state           = "onPageCreate", 
    data            = {}, 
    asyncAll        = true,
    asyncAllJoin    = false,
    asyncJoinTimeout = 30,
    asyncPriority   = "high"
);
```

