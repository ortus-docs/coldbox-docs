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
threadData = announceInterception(state="onLogin", interceptData={ user=user }, asyncAll=true);
```

Now that you have seen all asynchronous arguments and their features, let's investigate each use case one by one.

## Async Announcement

The first case involves where you want to completely detach an interception call into the background. You will accomplish this by using the async=true argument. This will then detach the execution of the interception into a separate thread and return to you a structure containing information about the thread it created for you. This is a great way to send work jobs, emails, processing and more to the background instead of having the calling thread wait for execution.

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, async=true);
```

### Configuration Arguments

You can also combine this call with the following arguments:
* asyncPrirority : The priority level of the detached thread. By default it uses normal priority level 

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, async=true, asyncPriority="low");
```
## Async Listeners With Join

The second use case is where you want to run the interception but multi-thread all the interceptor CFCs that are listening to that interception point. So let's say we have our onPageCreate announcement and we have 3 interceptors that are listening to that interception point. Then by using the asyncAll=true argument, ColdBox will create 3 separate threads, one for each of those interceptors and execute their methods in their appropriate threads. This is a great way to delegate long running processes simultaneously on a specific piece of data. Also, by default, the caller will wait for all of those 3 threads to finalize before continuing execution. So it is a great way to process data and wait until it has become available.

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, asyncAll=true);
```

### Configuration Arguments

You can also combine this call with the following arguments:
* asyncPrirority : The priority level of each of the spawned threads. By default it uses normal priority level 
* asyncJoinTimeout : The timeout in milliseconds to wait for all spawned threads. By default it is set to 0, which waits until ALL threads finalize no matter how long they take.
* asyncAllJoin : The flag that determines if the caller should wait for all spawned threads or should just spawn all threads and continue immediately. By default, it waits until all spawned threads finalize.

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, asyncAll=true, asyncAllJoin=false);
```

## Async Listeners No Join

The third use case is exactly the same scenario as above but with the exception that the caller will not wait for the spawned threads at all. This is accomplished by using the asyncAllJoin=false flag. This tells ColdBox to just spawn, return back a structure of thread information and continue execution of the calling code.

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, asyncAll=true, asyncAllJoin=false);
```

### Configuration Arguments

You can also combine this call with the following arguments:

* asyncPriority : The priority level of each of the spawned threads. By default it uses normal priority level 

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, asyncAll=true, asyncAllJoin=true, asyncPriority="high");
```

