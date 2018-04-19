# Async Listeners No Join

The third use case is exactly the same scenario as the async listeners but with the exception that the caller will **not** wait for the spawned threads at all. This is accomplished by using the `asyncAllJoin=false` flag. This tells ColdBox to just spawn, return back a structure of thread information and continue execution of the calling code.

```javascript
var threadData = announceInterception(
    state           = "onPageCreate", 
    interceptData   = {}, 
    asyncAll        = true, 
    asyncAllJoin    = false
);
```

## Configuration Arguments

You can also combine this call with the following arguments:

* `asyncPriority` : The priority level of each of the spawned threads. By default it uses `normal` priority level 

```javascript
var threadData = announceInterception(
    state           = "onPageCreate", 
    interceptData   = {}, 
    asyncAll        = true, 
    asyncAllJoin    = false,
    asyncPriority   = "high"
);
```

