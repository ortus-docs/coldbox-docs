# Parallel Computations

Here are some of the methods that will allow you to do parallel computations. Please note that the `asyncManager()` has shortcuts to these methods, but we always recommend using them via a new future, because then you can have further constructor options like: custom executor, debugging, loading CFML context and much more.

* `all( a1, a2, ... ):Future` : This method accepts an infinite amount of future objects,  closures or an array of closures/futures in order to execute them in parallel.  It will return a future that when you call `get()` on it, it will retrieve an array of the results of all the operations.
* `allApply( items, fn, executor ):array` : This function can accept an array of items or a struct of items of any type and apply a function to each of the item's in parallel.  The `fn` argument receives the appropriate item and must return a result.  Consider this a parallel `map()` operation.
* `anyOf( a1, a2, ... ):Future` : This method accepts an infinite amount of future objects, closures or an array of closures/futures and will execute them in parallel. However, instead of returning all of the results in an array like `all()`, this method will return the future that executes the fastest! Race Baby!
* `withTimeout( timeout, timeUnit )` : Apply a timeout to `all()` or `allApply()` operations.  The `timeUnit` can be: days, hours, microseconds, milliseconds, minutes, nanoseconds, and seconds. The default is milliseconds. 

{% hint style="info" %}
Please note that some of the methods above will return a ColdBox Future object that is backed by Java's CompletableFuture \([https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)\)
{% endhint %}

Here are the method signatures for the methods above, which you can call from the `asyncManager` or a newly created future.

```javascript
/**
 * This method accepts an infinite amount of future objects, closures or an array of future objects/closures
 * in order to execute them in parallel.  It will return back to you a future that will return back an array
 * of results from every future that was executed. This way you can further attach processing and pipelining
 * on the constructed array of values.
 *
 * <pre>
 * results = all( f1, f2, f3 ).get()
 * all( f1, f2, f3 ).then( (values) => logResults( values ) );
 * </pre>
 *
 * @result A future that will return the results in an array
 */
Future function all(){

/**
 * This function can accept an array of items or a struct and apply a function
 * to each of the item's in parallel.  The `fn` argument receives the appropriate item
 * and must return a result.  Consider this a parallel map() operation
 *
 * <pre>
 * // Array
 * allApply( items, ( item ) => item.getMemento() )
 * // Struct: The result object is a struct of `key` and `value`
 * allApply( data, ( item ) => item.key & item.value.toString() )
 * </pre>
 *
 * @items An array to process
 * @fn The function that will be applied to each of the array's items
 * @executor The custom executor to use if passed, else the forkJoin Pool
 *
 * @return An array with the items processed
 */
any function allApply( any items, required fn, executor ){

/**
 * This method accepts an infinite amount of future objects or closures and will execute them in parallel.
 * However, instead of returning all of the results in an array like all(), this method will return
 * the future that executes the fastest!
 *
 * <pre>
 * // Let's say f2 executes the fastest!
 * f2 = anyOf( f1, f2, f3 )
 * </pre>
 *
 * @return The fastest executed future
 */
Future function anyOf()

/**
 * This method seeds a timeout into this future that can be used by the following operations:
 *
 * - all()
 * - allApply()
 *
 * @timeout The timeout value to use, defaults to forever
 * @timeUnit The time unit to use, available units are: days, hours, microseconds, milliseconds, minutes, nanoseconds, and seconds. The default is milliseconds
 *
 * @returns This future
 */
Future function withTimeout( numeric timeout = 0, string timeUnit = "milliseconds" )
```

Here are some examples:

```javascript
// Let's find the fastest dns server
var f = asyncManager().anyOf( ()=>dns1.resolve(), ()=>dns2.resolve() );

// Let's process some data
var data = [1,2, ... 100 ];
var results = asyncManager().all( data );

// Process multiple futures
var f1 = asyncManager.newFuture( function(){
    return "hello";
} );
var f2 = asyncManager.newFuture( function(){
    return "world!";
} );
var aResults = asyncManager.newFuture()
    .withTimeout( 5 )
    .all( f1, f2 );

// Process mementos for an array of objects
function index( event, rc, prc ){
    return async().allApply(
        orderService.findAll(),
        ( order ) => order.getMemento()
    );
}
```

### Custom Executors

Please also note that you can choose your own Executor for the parallel computations by passing the `executor` via the `newFuture()` method.

```javascript
var data = [ 1 ... 5000 ];
var results = newFuture( executor : asyncManager.$executors.newCachedThreadPool() )
    .all( data );

// Process mementos for an array of objects on a custom pool
function index( event, rc, prc ){
    return async().allApply(
        orderService.findAll(),
        ( order ) => order.getMemento(),
        async().$executors.newFixedThreadPool( 50 )
    );
}
```

