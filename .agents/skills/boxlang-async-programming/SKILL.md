---
name: boxlang-async-programming
description: "Use this skill when writing BoxLang asynchronous code: BoxFuture, futureNew, asyncRun, asyncAll, asyncAny, asyncAllApply, executors, schedulers, thread components, parallel pipelines, file watchers, or distributed locking with bx:lock."
---

# BoxLang Async Programming

## Overview

BoxLang provides a comprehensive async framework built on Java's CompletableFuture
and Project Loom virtual threads. The `AsyncService` manages executors, schedulers,
and futures. All async primitives integrate seamlessly with the BoxLang runtime.

## Creating BoxFutures

`BoxFuture` extends `CompletableFuture` with BoxLang-friendly chaining.

### `futureNew()` — Primary BIF (v1.4.0+)

```boxlang
// Create a future from a function (runs asynchronously)
var future = futureNew( () => fetchDataFromAPI() )

// Create a completed future with a value
var future = futureNew( "Hello World" )

// Create an empty future (complete later)
var future = futureNew()

// Create with a specific executor
var future = futureNew( () => heavyCalculation(), "cpu-tasks" )
```

### `asyncRun()` — Simplified Async Execution

```boxlang
// Run a function asynchronously on the default io-tasks executor
var future = asyncRun( () => fetchDataFromAPI() )

// Run with specific executor
var future = asyncRun( () => processCPUWork(), "cpu-tasks" )

// Get the result (blocks until complete)
var result = future.get()

// With timeout
var result = future.get( 5, "seconds" )
```

## Chaining with `then`, `thenAsync`, and `onError`

- `then()` — runs the transformation on the **same thread** (fast, lightweight transforms)
- `thenAsync()` — runs the transformation on an **executor thread** (for heavy/I/O work)

```boxlang
asyncRun( () => fetchUser( userId ) )
    .then( (user) => enrichWithProfile( user ) )          // same thread
    .thenAsync( (user) => sendWelcomeEmail( user ) )      // executor thread
    .then( (user) => user )
    .onError( (error) => {
        logError( error.message )
        return getDefaultUser()
    })
    .get()
```

## Core Result Methods

```boxlang
// Blocking retrieval
var result = future.get()
var result = future.get( 5000 )              // timeout in ms
var result = future.get( 5, "seconds" )      // timeout with unit

// Safe retrieval with defaults
var result = future.getOrDefault( "fallback" )
var result = future.joinOrDefault( 0 )       // join() variant with default

// Get as Attempt object (functional error handling)
var attempt = future.getAsAttempt()
if ( attempt.isPresent() ) {
    doSomething( attempt.get() )
}
```

## Parallel Execution with `asyncAll`

`asyncAll()` runs multiple operations concurrently and returns a `BoxFuture<Array>` —
call `.get()` once to receive all results in order:

```boxlang
// Returns BoxFuture<Array> — .get() resolves to [result1, result2, result3]
var results = asyncAll([
    () => fetchOrders( userId ),
    () => fetchProfile( userId ),
    () => fetchPreferences( userId )
]).get()

var [orders, profile, prefs] = results

// Mix lambdas, closures, and pre-created futures
var results = asyncAll([
    () => fetchOrders( userId ),           // function
    futureNew( () => fetchProfile( id ) ), // pre-created future
    () => fetchPreferences( userId )       // function
]).get()
```

## Race to the Finish with `asyncAny`

`asyncAny()` returns the result of whichever future completes **first** (v1.4.0+):

```boxlang
var fastestResult = asyncAny([
    () => fetchFromPrimaryDB(),
    () => fetchFromReplicaDB(),
    () => fetchFromCache()
]).get()
```

## Parallel Collection Processing with `asyncAllApply`

Apply a function to every element of an array or struct **in parallel** (v1.4.0+):

```boxlang
// Array processing in parallel
var userIds = [ 1, 2, 3, 4, 5 ]
var profiles = asyncAllApply(
    userIds,
    ( id ) => fetchUserProfile( id )  // each item processed in parallel
)
// profiles = [ profile1, profile2, profile3, profile4, profile5 ]

// Struct processing in parallel
var config = { db: "prod-db", cache: "redis", queue: "rabbit" }
var validated = asyncAllApply(
    config,
    ( item ) => validateConfig( item.key, item.value )  // item = { key, value }
)
```

## Named Executors

```boxlang
// Three pre-configured runtime executors:
// "io-tasks"        — virtual threads (default, best for I/O-bound work)
// "cpu-tasks"       — scheduled pool, 20 threads (best for CPU-bound work)
// "scheduled-tasks" — scheduled pool, 20 threads (for cron/periodic tasks)

// Pass executor name as second arg to asyncRun / futureNew
asyncRun( () => cpuIntensiveWork(), "cpu-tasks" )
futureNew( () => fetchData(), "io-tasks" )

// Access an executor by name
var executor = executorGet( "io-tasks" )
```

## Async Pipelines

```boxlang
// Sequential pipeline
var result = futureNew( () => loadRawData() )
    .then( (data) => parseData( data ) )
    .then( (parsed) => validate( parsed ) )
    .then( (valid) => persist( valid ) )
    .onError( (e) => rollback() )
    .get()

// Mix sequential and parallel stages
var pipeline = asyncRun( () => fetchConfig() )
    .then( (config) => {
        // Fan out: run two tasks in parallel with the config
        var results = asyncAll([
            () => buildReport( config ),
            () => sendNotifications( config )
        ]).get()
        return results
    })
    .get()
```

## Scheduled Tasks

Create a BoxLang Scheduler class (`Scheduler.bx`) and register it in `boxlang.json`:

```boxlang
// schedulers/MyScheduler.bx
class {

    property name="scheduler"
    property name="logger"

    function configure() {
        scheduler.setSchedulerName( "MyApp-Scheduler" )
        scheduler.setTimezone( "UTC" )

        // Register tasks with fluent DSL
        scheduler.task( "cleanupExpiredSessions" )
            .call( () => sessionService.cleanup() )
            .every( 15, "minutes" )
            .onFailure( (task, error) => logError( error.message ) )

        scheduler.task( "dailyReport" )
            .call( () => reportService.generate() )
            .every( 1, "day" )
            .startOn( "00:00" )
    }

    void function onStartup() {
        logger.info( "Scheduler started: #scheduler.getSchedulerName()#" )
    }

    void function onShutdown() {
        logger.info( "Scheduler shutting down" )
    }

    void function onAnyTaskError( required task, required exception ) {
        logger.error( "Task '#task.getName()#' failed: #exception.message#" )
    }
}
```

Register in `boxlang.json`:

```json
{
    "scheduler": {
        "schedulers": [ "/path/to/schedulers/MyScheduler.bx" ]
    }
}
```

Or run from CLI:

```bash
boxlang schedule /path/to/schedulers/MyScheduler.bx
```

## The `thread` Component

For explicit thread management:

```boxlang
// Start a named thread
thread name="backgroundWorker" action="run" {
    // Code runs in a new thread
    processLargeDataset( datasetId )
}

// Start multiple threads
thread name="worker1" action="run" {
    processChunk( chunk1 )
}
thread name="worker2" action="run" {
    processChunk( chunk2 )
}

// Wait for threads to finish
thread action="join" name="worker1,worker2" timeout=30000

// Access thread results
var result1 = cfthread.worker1.result
var result2 = cfthread.worker2.result
```

## Distributed Locking with `bx:lock`

`bx:lock` prevents race conditions and supports distributed locking:

```boxlang
// Exclusive lock (one thread at a time)
bx:lock name="userUpdate_#userId#" type="exclusive" timeout=10 {
    // Only one request can be here at a time for this userId
    user = userService.update( userId, data )
}

// Read lock (multiple readers, exclusive writer)
bx:lock name="configCache" type="readonly" timeout=5 {
    var config = cacheGet( "appConfig" )
}
bx:lock name="configCache" type="exclusive" timeout=5 {
    cachePut( "appConfig", freshConfig )
}

// Scoped locks (application, session, request)
bx:lock scope="application" type="exclusive" timeout=10 {
    if ( !application.initialized ) {
        initializeApp()
        application.initialized = true
    }
}
```

## File and Directory Watchers (v1.12+)

```boxlang
// Watch a directory for changes
var watcher = directoryWatcher(
    path     = expandPath( "./config" ),
    onChange = (event) -> {
        // event.type: "create", "modify", "delete"
        // event.path: full path to changed file
        reloadConfig( event.path )
    },
    filter   = "*.json",
    recurse  = false
)

watcher.start()

// Stop later
watcher.stop()
```

## Error Handling in Async Code

```boxlang
// onError continues the chain with a recovery value
var result = asyncRun( () => flakyOperation() )
    .onError( (err) => {
        logError( err.message )
        return defaultValue   // chain continues with this
    })
    .then( (val) => process( val ) )
    .get()

// exceptionally (Java-style)
var future = asyncRun( () => riskyWork() )
future.exceptionally( (err) => fallback )

// Check completion state
if ( future.isDone() ) { ... }
if ( future.isCompletedExceptionally() ) { ... }
if ( future.isCancelled() ) { ... }

// Cancel a future
future.cancel( true )
```

## Async with Timeout and Fallback

```boxlang
// orTimeout — throws TimeoutException after delay
var result = asyncRun( () => slowExternalAPI() )
    .orTimeout( 3, "seconds" )
    .onError( (err) => getCachedResult() )
    .get()

// completeOnTimeout — resolves with default value instead of throwing
var result = asyncRun( () => slowExternalAPI() )
    .completeOnTimeout( getDefaultData(), 3, "seconds" )
    .get()
```

## Virtual Threads Best Practices

```boxlang
// Virtual threads (io-tasks, default) — ideal for:
// - HTTP calls, database queries, file I/O, any blocking I/O
asyncRun( () => httpClient.get( url ) )             // good — io-tasks
asyncRun( () => queryExecute( sql ) )               // good — io-tasks

// CPU pool — ideal for CPU-intensive work:
// - Image processing, encryption, data transformation
asyncRun( () => encryptLargeFile( path ), "cpu-tasks" )
futureNew( () => processLargeDataset( data ), "cpu-tasks" )
```

## Executor Types and Runtime Configuration

### Pre-Configured Runtime Executors

BoxLang provides three pre-configured executors accessible via `executorGet()`:

```boxlang
// Access pre-configured runtime executors
var ioExecutor        = executorGet( "io-tasks" )        // Virtual threads for I/O
var cpuExecutor       = executorGet( "cpu-tasks" )       // Scheduled pool for CPU work
var scheduledExecutor = executorGet( "scheduled-tasks" ) // For scheduled tasks

// Use directly
var future = ioExecutor.submit( () => fetchFromAPI( url ) )
var result = future.get()
```

**Runtime Executor Configuration** (`boxlang.json`):

```json
{
    "executors": {
        "io-tasks":        { "type": "virtual" },
        "cpu-tasks":       { "type": "scheduled", "threads": 10 },
        "scheduled-tasks": { "type": "scheduled", "threads": 10 }
    }
}
```

### Creating Custom Executors

BoxLang supports seven executor types via `executorNew()`:

```boxlang
// Virtual threads — best for I/O-bound tasks (default, JVM 21+)
var ioPool = executorNew( "virtual", "my-io-pool" )

// Fixed thread pool — CPU-bound tasks with predictable concurrency
var cpuPool = executorNew( type="fixed", name="cpu-pool", threads=8 )

// Cached pool — grows/shrinks dynamically for bursty workloads
var dynPool = executorNew( "cached", "burst-pool" )

// Single thread — sequential guaranteed-order execution
var seqPool = executorNew( "single", "sequential-processor" )

// Fork-join — recursive divide-and-conquer algorithms
var fjPool = executorNew( type="fork_join", name="fj-pool", parallelism=8 )

// Work-stealing — auto load-balancing across threads
var wsPool = executorNew( type="work_stealing", name="load-balanced", parallelism=8 )
```

### Scheduled Execution API

The `scheduled` executor type provides `scheduleOnce`, `scheduleAtFixedRate`, and `scheduleWithFixedDelay`:

```boxlang
var executor = executorNew( type="scheduled", name="scheduler", threads=5 )

// Run once after a delay
var future = executor.scheduleOnce(
    () => performMaintenance(),
    5,        // delay value
    "seconds" // time unit
)

// Run at a fixed rate (regardless of task duration)
var future = executor.scheduleAtFixedRate(
    () => healthCheck(),
    0,        // initial delay
    30,       // period
    "seconds"
)

// Run with fixed delay BETWEEN executions (waits for task to finish first)
var future = executor.scheduleWithFixedDelay(
    () => processQueue(),
    10,       // initial delay
    5,        // delay between completions
    "seconds"
)

// Submit a callable and get result
var resultFuture = executor.submit( () => complexCalculation() )
var result = resultFuture.get()

// Shutdown executor when done
executor.shutdown()
```

### Executor Statistics

```boxlang
var stats = executor.getStatistics()
writeOutput( "Active threads:   #stats.activeCount#" )
writeOutput( "Completed tasks:  #stats.completedTaskCount#" )
writeOutput( "Pool size:        #stats.poolSize#" )
writeOutput( "Is shutdown:      #executor.isShutdown()#" )
```

## References

- [Async Programming](https://boxlang.ortusbooks.com/boxlang-framework/async-programming)
- [Scheduling](https://boxlang.ortusbooks.com/boxlang-framework/scheduled-tasks)
- [Threading](https://boxlang.ortusbooks.com/boxlang-language/threading)
- [Locking](https://boxlang.ortusbooks.com/boxlang-language/locking)