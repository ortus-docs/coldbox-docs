# Interceptor Asynchronicity

In honor of one of my favorite bands and album, [The Police](http://en.wikipedia.org/wiki/Synchronicity_(The_Police_album) - [Synchronicity](http://www.youtube.com/watch?v=CMBufJmTTSA), we have some asynchrounous capabilities in ColdBox Interceptors. These features are thanks to the sponsorship of [Guardly](https://www.guardly.com/), Inc, Alert, Connect, Stay Safe. So please make sure to check them out and thank them for sponsoring this great feature set. The core interceptor service and announcement methods have some arguments that can turn asynchronicity on or off and can return a structure of threading data.

```js
any announceInterception(state, interceptData, async, asyncAll, asyncAllJoin, asyncJoinTimeout, asyncPriority);
```

The asynchronous arguments are listed in the table below:

|Argument|Type|Required|Default Value|Description|
|--|--|--|--|--|
|async|boolean|false|false|Threads the interception call so the entire execution chain is ran in a separate thread. Extra arguments that can be used: asyncPriority.|
|asyncAll |boolean |false|false|Mutually exclusive with the async argument. If true, this will execute the interception point but multi-thread each of the CFCs that are listening to the interception point. So if 3 CFCs are listening, then 3 separate threads will be created for each CFC call. By default, the calling thread will wait for all 3 separate threads to finalize in order to continue with the execution. Extra arguments that can be used: *asyncAllJoin*, *asyncTimeout*, *asyncPriority*.|
|asyncAllJoin |boolean |false|true|This flag is used only when combined with the asyncAll argument. If true (default), the calling thread will wait for all intercepted CFC calls to execute. If false, then the calling thread will not join the multi-threaded interception and continue immediate execution while the CFC's continue to execute in the background.|
|asyncPriority |string : *low,normal,high* |false|normal|The thread priority that will be sent to each *cfthread* call that is made by the system.|
|asyncJoinTimeout |numeric|false|0|This argument is only used when using the *asyncAll* and *asyncAllJoin=true* arguments. This argument is the number of milliseconds the calling thread should wait for all the threaded CFC listeners to execute. By default it waits until all threads finalize. |

All asynchronous calls will return a structure of thread information back to you when using the `announceInterception()` method or directly in the interceptor service, the `processState()` method. The structure contains information about all the threads that where created during the call and their respective information like: status, data, errors, monitoring, etc.


```js
threadData = announceInterception(
    state           = "onLogin", 
    interceptData   = { user=user }, 
    asyncAll        = true
);
```

Now that you have seen all asynchronous arguments and their features, let's investigate each use case one by one.