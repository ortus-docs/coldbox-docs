---
description: To The Future with ColdBox Futures
---

# Async Pipelines & Futures

## Creation Methods

You will be able to create async pipelines and futures by using the following `AsyncManager` creation methods:

* `init( value, executor, debug, loadAppContext )` :  Construct a new future. The `value` argument can be the future closure/udf.  You can also pass in a custom executor and some utility flags.
* `newFuture( [task], [executor] ):Future` : Returns a ColdBox Future. You can pass an optional task \(closure/udf\) and even an optional executor.
* `newCompletedFuture( value ):Future` : Returns a new future that is already completed with the given value.

{% hint style="info" %}
Please note that some of the methods above will return a ColdBox Future object that is backed by Java's `CompletableFuture` \([https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html)\)
{% endhint %}

Here are the method signatures for the methods above:

```javascript
/**
 * Create a new ColdBox future backed by a Java completable future
 *
 * @value The actual closure/lambda/udf to run with or a completed value to seed the future with
 * @executor A custom executor to use with the future, else use the default
 * @debug Add debugging to system out or not, defaults is false
 * @loadAppContext Load the CFML engine context into the async threads or not, default is yes.
 *
 * @return ColdBox Future completed or new
 */
Future function newFuture(
    any value,
    any executor,
    boolean debug          = false,
    boolean loadAppContext = true
)

/**
 * Create a completed ColdBox future backed by a Java Completable Future
 *
 * @value The value to complete the future with
 * @debug Add debugging to system out or not, defaults is false
 * @loadAppContext Load the CFML engine context into the async threads or not, default is yes.
 *
 * @return ColdBox Future completed
 */
Future function newCompletedFuture(
    required any value,
    boolean debug          = false,
    boolean loadAppContext = true
)
```

### Usage

There are two ways to start async computations with futures:

1. Via the `newFuture()` constructor
2. Via the `run()` method

The constructor is the shortcut approach and only allows for closures to be defined as the task. The `run()` methods allows you to pass a CFC instance and a `method` name, which will then call that method as the initial computation.

```javascript
// Constructor
newFuture( () => newUser() )

// Run Operations
newFuture().run( () => newUser() )
newFuture().run( supplier : userService, method : "newUser" )
```

Here are the `run()` method signatures:

```javascript
/**
 * Executes a runnable closure or component method via Java's CompletableFuture and gives you back a ColdBox Future:
 *
 * - This method calls `supplyAsync()` in the Java API
 * - This future is asynchronously completed by a task running in the ForkJoinPool.commonPool() with the value obtained by calling the given Supplier.
 *
 * @supplier A CFC instance or closure or lambda or udf to execute and return the value to be used in the future
 * @method If the supplier is a CFC, then it executes a method on the CFC for you. Defaults to the `run()` method
 * @executor An optional executor to use for asynchronous execution of the task
 *
 * @return The new completion stage (Future)
 */
Future function run(
    required supplier,
    method       = "run",
    any executor = variables.executor
)
```

{% hint style="warning" %}
Please note that the majority of methods take in an `executor` that you can supply. This means that you can decide in which thread pool the task will execute in, or by default it will run in the `ForkJoinPool` or the same thread the computation started from.
{% endhint %}

{% hint style="danger" %}
**WARNING:** Once you pass a closure/udf or cfc/method to the `run()` methods or the constructor, the JDK will create and send the task for execution to the appropriate executor.  You do not need to start the thread or issue a start command.  It is implied.
{% endhint %}

## ColdFusion \(CFML\) App Context

The `loadAppContext` is a boolean flag that allows you to load the ColdFusion \(CFML\) application context into the running threads.  By default, this is needed if your threads will require certain things from the application context: mappings, app settings, etc.  However, some times this can cause issues and slowdowns, so be careful when selecting when to load the context or not.  As a rule of thumb, we would say to NOT load it, if you are doing pure computations or not requiring mappings or app settings.

For example, the ColdBox file logger uses futures and has no app context loaded, since it does not require it.  It only monitors a log queue and streams the content to a file.  Remember, cohesion and encapsulation.  The purerer your computations are, the better and safer they will be \([https://en.wikipedia.org/wiki/Pure\_function](https://en.wikipedia.org/wiki/Pure_function)\)

## Completed Futures

There are also times where you need a future to be completed immediately. For this you can build a future already completed via the `newCompletedFuture()` or by leveraging the `complete()` function in the Future object.  This can also allow you to complete the future with an initial value if you want to.

```javascript
f = newCompletedFuture( 100 );

f = newFuture( () => orderService.getData() )
    .complete( initialValue )
    .then()
    .get()
```

{% hint style="success" %}
Completed futures are great for mocking and testing scenarios
{% endhint %}

## Usage Methods

There are many many methods in the Java JDK that are implemented in the ColdBox Futures.  We have also added several new methods that play very nicely with our dynamic language.  Here is a collection of the currently implemented methods.

{% hint style="warning" %}
Always checkout the API docs for the latest methods and signatures: [https://apidocs.ortussolutions.com/coldbox/current](https://apidocs.ortussolutions.com/coldbox/current)
{% endhint %}

<table>
  <thead>
    <tr>
      <th style="text-align:left">Method</th>
      <th style="text-align:left">Returns</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">all()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">Allows for the parallel execution of many closures/futures or an array
        of closures/futures.</td>
    </tr>
    <tr>
      <td style="text-align:left">allApply()</td>
      <td style="text-align:left">Collection</td>
      <td style="text-align:left">Allows you to apply a function to every element of a collection: array
        or struct and then reconstructing the collection according to your changes.
        A parallel map()</td>
    </tr>
    <tr>
      <td style="text-align:left">anyOf()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">Allows for the parallel/execution of many closures/futures or an array
        of closures/futures, but the only returning the FASTEST future that completes.
        The race is on!</td>
    </tr>
    <tr>
      <td style="text-align:left">cancel()</td>
      <td style="text-align:left">Boolean</td>
      <td style="text-align:left">If not already completed, completes this Future with a <code>CancellationException</code>.Dependent
        Futures that have not already completed will also complete exceptionally,
        with a <code>CompletionException</code> caused by this <code>CancellationException</code>.</td>
    </tr>
    <tr>
      <td style="text-align:left">complete()</td>
      <td style="text-align:left">Boolean</td>
      <td style="text-align:left">If not already completed, sets the value returned by get() and related
        methods to the given value.</td>
    </tr>
    <tr>
      <td style="text-align:left">completedFuture()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">Returns a new ColdBox Future that is already completed with the given
        value.</td>
    </tr>
    <tr>
      <td style="text-align:left">completeExceptionally()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">If not already completed, causes invocations of get() and related methods
        to throw the given exception. The exception type is of <code>java.lang.RuntimeException</code> and
        you can choose the message to throw with it.</td>
    </tr>
    <tr>
      <td style="text-align:left">exceptionally()
        <br />onException()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">
        <p>Register an event handler for any exceptions that happen before it is
          registered in the future pipeline.</p>
        <p>Whatever this function returns, will be used for the next registered functions
          in the pipeline.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">get()</td>
      <td style="text-align:left">any</td>
      <td style="text-align:left">Waits if necessary for at most the given time for this future to complete,
        and then returns its result, if available.</td>
    </tr>
    <tr>
      <td style="text-align:left">getNative()</td>
      <td style="text-align:left">Java CompletableFuture</td>
      <td style="text-align:left">Get the native Java CompletableFuture</td>
    </tr>
    <tr>
      <td style="text-align:left">getNow()</td>
      <td style="text-align:left">any</td>
      <td style="text-align:left">Returns the result value (or throws any encountered exception) if completed,
        else returns the given defaultValue.</td>
    </tr>
    <tr>
      <td style="text-align:left">isCancelled()</td>
      <td style="text-align:left">Boolean</td>
      <td style="text-align:left">Flag that checks if the computation has been cancelled</td>
    </tr>
    <tr>
      <td style="text-align:left">isCompletedExceptionally()</td>
      <td style="text-align:left">Boolean</td>
      <td style="text-align:left">Flag that checks if the computation threw an exception</td>
    </tr>
    <tr>
      <td style="text-align:left">isDone()</td>
      <td style="text-align:left">Boolean</td>
      <td style="text-align:left">Flag that checks if the computation has finished</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>run()
          <br />runAsync()</p>
        <p>supplyAsync()</p>
      </td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">Executes a runnable closure or component method via Java&apos;s CompletableFuture
        and gives you back a ColdBox Future:</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>then()</p>
        <p>thenApply()</p>
      </td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">Executed once the computation has finalized and a result is passed in
        to the target</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>thenAsync()</p>
        <p>thenApplyAsync()</p>
      </td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">Executed once the computation has finalized and a result is passed in
        to the target but this will execute in a separate thread. By default it
        uses the <code>ForkJoin.commonPool() </code>but you can pass your own executor
        service.</td>
    </tr>
    <tr>
      <td style="text-align:left">thenCombine()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">This used when you want two Futures to run independently and do something
        after both are complete.</td>
    </tr>
    <tr>
      <td style="text-align:left">thenCompose()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">
        <p></p>
        <p>Returns a new CompletionStage that, when this stage completes normally,
          is executed with this stage as the argument to the supplied function.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">withTimeout()</td>
      <td style="text-align:left">Future</td>
      <td style="text-align:left">Ability to attach a timeout to the execution of the allApply() method</td>
    </tr>
  </tbody>
</table>## Execution Status

## Getting Values

## Future Pipelines

## Cancelling Futures

## Exceptions

## Combining Futures

## Composing Futures

