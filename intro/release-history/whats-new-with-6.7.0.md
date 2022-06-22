---
description: June 21, 2022
---

# What's New With 6.7.0

## Major Updates

Here is a listing of all the major updates and improvements in this version.

### Event Caching HTTP Response Codes

![](https://cdn0.iconfinder.com/data/icons/thin-line-icons-for-seo-and-development-1/64/Programming\_Development\_Api-128.png)

Event caching has been updated to allow the caching of the set http response code from your handler code via `event.setHTTPHeader()` or `event.renderData()` .  This is essential from a developer's perspective as it will respect whatever response code you respond with.  This is also imperative for RESTFul services.

```javascript
function data( event, rc, prc ) cache=true cacheTimeout=5{
    
    event.setHTTPHeader( statusCode = 404 );

}
```

### WireBox Performance, Performance and More Performance

![Performance, stopwatch, timer, speed, time, time management](https://cdn2.iconfinder.com/data/icons/thin-line-icons-for-seo-and-development-1/64/SEO\_stopwatch\_timer\_performance-128.png)

This release brings in a complete re-architecture of the creation, inspection and wiring of objects in WireBox in order to increase performance.  Every single line of code was optimized and analyzed in order to bring the creation, inspection and wiring of objects to its maximum speed.  This will be noted more on the creation of transient (non-persisted) objects more than in singleton objects.  So if you are asking WireBox for transient objects, you will see and feel the difference.

In some of our performance testing we had about 4000 object instantiations running between 500ms-1,100 ms depending on CPU load. While with simple `createObject()`  and no wiring, they click around 400-700 ms.  Previously, we had the same instantiations clocking at 900-3,500 ms.  So we can definitely see a major improvement in this area.

### Scheduled Tasks Exception Handling

![](https://cdn2.iconfinder.com/data/icons/mobile-smart-phone/64/search\_error\_inspect\_phone\_ios11\_iphone-128.png)

All scheduled tasks have automatic exception handling now.  Before, whenever you had exceptions and you did **NOT** implement any of the exception handling listeners, your code would be swallowed up and never to be seen!  Now, we avoid this and log them to standard error so you can debug your code.

Also, in the previous version, if you had an exception your `afterAnyTask()` or the `after()` life cycle methods would never be called. Now they are!! Hooray!!

### ColdBox Schedulers Automatic Injection

![](https://cdn1.iconfinder.com/data/icons/carbon-design-system-vol-3/32/column-dependency-128.png)

All ColdBox enabled schedulers will have the following automatic injections so you can have ease of use for leveraging objects and contexts during your task declarations and executables.



| **Property**          | **Description**                                                                                               |
| --------------------- | ------------------------------------------------------------------------------------------------------------- |
| `controller`          | The ColdBox running app controller                                                                            |
| `cachebox`            | The CacheBox reference                                                                                        |
| `wirebox`             | The WireBox reference                                                                                         |
| `log`                 | A configured LogBox logger                                                                                    |
| `coldboxVersion`      | The ColdBox version you are running                                                                           |
| `appMapping`          | The ColdBox app mapping                                                                                       |
| `getJavaSystem()`     | Function to get access to the java system                                                                     |
| `getSystemSetting()`  | Retrieve a Java System property or env value by name. It looks at properties first then environment variables |
| g`etSystemProperty()` | Retrieve a Java System property value by key                                                                  |
| `getEnv()`            | Retrieve a Java System environment value by name                                                              |



All **module schedulers** will have the following extra automatic injections:



| **Property**     | **Description**                 |
| ---------------- | ------------------------------- |
| `moduleMapping`  | The module’s mapping            |
| `modulePath`     | The module’s path on disk       |
| `moduleSettings` | The module’s settings structure |



### Scheduled Tasks Start and End Dates

![Calendar, date, event, month](https://cdn3.iconfinder.com/data/icons/linecons-free-vector-icons-pack/32/calendar-128.png)

All scheduled tasks now support the ability to seed in the start and end dates via our DSL:

* `startOn( date, time = "00:00" )`
* `endOn( data, time = "00:00" )`

This means that you can tell the scheduler when the task will become active on a specific data and time (using the scheduler's timezone), and when the task will become disabled.

```javascript
task( "restricted-task" )
  .call( () => ... )
  .everyHour()
  .startOn( "2022-01-01", "00:00" )
  .endOn( "2022-04-01" )
```

### xTask() - Easy Disabling of Tasks

![](https://cdn2.iconfinder.com/data/icons/user-interface-essential-solid/32/Artboard\_57-128.png)

Thanks to the inspiration of [TestBox](https://testbox.ortusbooks.com/) where you can mark a spec or test to be skipped from execution by prefixing it with the letter `x` you can now do the same for any task declaration.  If they are prefixed with the letter `x` they will be registered but **disabled** automatically for you.

```javascript
function configure(){

	xtask( "Disabled Task" )
		.call ( function(){
			writeDump( var="Disabled", output="console" );
		})
		.every( 1, "second" );

	task( "Scope Test" )
		.call( function(){
			writeDump( var="****************************************************************************", output="console" );
			writeDump( var="Scope Test (application) -> #getThreadName()# #application.keyList()#", output="console" );
			writeDump( var="Scope Test (server) -> #getThreadName()# #server.keyList()#", output="console" );
			writeDump( var="Scope Test (cgi) -> #getThreadName()# #cgi.keyList()#", output="console" );
			writeDump( var="Scope Test (url) -> #getThreadName()# #url.keyList()#", output="console" );
			writeDump( var="Scope Test (form) -> #getThreadName()# #form.keyList()#", output="console" );
			writeDump( var="Scope Test (request) -> #getThreadName()# #request.keyList()#", output="console" );
			writeDump( var="Scope Test (variables) -> #getThreadName()# #variables.keyList()#", output="console" );
			writeDump( var="****************************************************************************", output="console" );
		} )
		.every( 60, "seconds" )
		.onFailure( function( task, exception ){
			writeDump( var='====> Scope test failed (#getThreadName()#)!! #exception.message# #exception.stacktrace.left( 500 )#', output="console" );
		} );
		
}
```

### Scheduled Tasks Singular Time Units

![](https://cdn3.iconfinder.com/data/icons/essential-pack-2/48/16-Time-128.png)

Every time unit can now be used as plural or singular, so it can allow you to create beautiful scheduled task DSLs:

```json
// Before
task( "my-task" )
	.call( () => {} )
	.every( 1, 'hours' );

// Which sounds weird when you read it. So now you can use singular

task( "my-task" )
	.call( () => {} )
	.every( 1, 'hour' );
```

#### Available Time Units

* Nanosecond(s)
* Microsecond(s)
* Millisecond(s)
* Second(s)
* Minute(s)
* Hour(s)
* Day(s)

### Safe Shutdown of Executors and Schedulers

![](https://cdn0.iconfinder.com/data/icons/car-seat-belt-and-airbag/205/seatbelt-008-128.png)

All executors and schedulers can now be shutdown more gracefully by passing a `timeout` argument to the async manager and automatically by the framework. This will allow the executor to shutdown and gracefully yell at it's tasks to shutdown in a default period of 30 seconds.  It will wait and then try again, if not, then well, you can't directly kill anything anymore, so you will be notified so you can do harsher punishments to these tasks.

The only exception this is **NOT** the case in a normal ColdBox app is when a reinit happens or when integration testing is being executed.  Then we revert to the previous behavior of nuking the executors and schedulers.

**AsyncManager**

* `shutdownAllExecutors( force, timeout )`
* `shutdownExecutor( name, force, timeout)`

**The timeout ONLY works when the `force` argument is false.  If `force` is true, then it's NOT gracefully shutdown. This usually happens on ColdBox reinits or integration testing.**

****

**Schedulers**

All schedulers have a `shutdownTimeout` property that defaults to 30 seconds.  When you configure your schedulers you can change this value to whatever you see fit.

```javascript
function configure(){
    
    // Set shutdown timeout to 60 seconds
    setShutdownTimeout( 60 );

}
```



**Executors - shutdown and wait...**

All executors now have a new method: `shutdownAndAwaitTermination( numeric timeout = 30 )` which is used to do just that. It places the executor in shutdown mode and waits the timeout for all the tasks to complete. If they don't complete then it issues a forced shutdown.

### forAttribute() - Integrate with JS Frameworks Easily

![Vue.JS, Alpine, React, Angular, etc.](https://cdn3.iconfinder.com/data/icons/fluent-regular-24px-vol-4/24/ic\_fluent\_javascript\_24\_regular-128.png)

All handlers/layouts and views get a new function called `forAttribute( data )`which will allow you to serialize simple or complex data so it can be used within HTML attributes.  This will take care of serialize the data and encoding it correctly so it can be bound to the HTML attribute so the JavaScript framework can use it as native JSON.

```html
<div>
    <User :data="#forAttribute( prc.user.getMemento() )#">
</div>
```

This technique will allow you to bridge your CFML apps with your JS apps natively.

### Async Interceptors Data

![](https://cdn4.iconfinder.com/data/icons/essential-app-1/16/cluster-data-group-organize-128.png)

Finally in this version of ColdBox asynchronous interceptors will work with any complex data without any thread contingency or duplication.  You can even use them for ORM events and it will work accordingly.  So go for it, [asynchronize](../../the-basics/interceptors/interceptor-asynchronicity/) your interception calls!

```javascript
threadData = announce(
    state           = "onPageCreate", 
    data            = { page= local.page }, 
    asyncAll        = true,
    asyncPriority   = "high"
);
```

### ORM Event Handling

![Server, proxy](https://cdn1.iconfinder.com/data/icons/carbon-design-system-vol-7/32/server--proxy-128.png)

This was a rough regression due to the way Hibernate is loaded by the CFML engines.  We have moved to a lazy load first strategy on the entire architecture of the framework.  So anything using the ColdBox proxy, like the ORM event handling, will now work in any loading situation.





## Release Notes

{% tabs %}
{% tab title="ColdBox HMVC" %}
**Bug**

* [COLDBOX-1114](https://ortussolutions.atlassian.net/browse/COLDBOX-1114) Persistence of variables failing due to null support
* [COLDBOX-1110](https://ortussolutions.atlassian.net/browse/COLDBOX-1110) Renderer is causing coldbox RestHandler to render convention view
* [COLDBOX-1109](https://ortussolutions.atlassian.net/browse/COLDBOX-1109) Exceptions in async interceptors are missing onException announcement
* [COLDBOX-1105](https://ortussolutions.atlassian.net/browse/COLDBOX-1105) Interception with `async` annotation causes InterceptorState Exception on Reinit
* [COLDBOX-1104](https://ortussolutions.atlassian.net/browse/COLDBOX-1104) A view not set exception is thrown when trying to execution handler ColdBox methods that are not concrete actions when they should be invalid events.
* [COLDBOX-1103](https://ortussolutions.atlassian.net/browse/COLDBOX-1103) Update getServerIP() so it avoids looking at the cgi scope as it can cause issues on ACF
* [COLDBOX-1100](https://ortussolutions.atlassian.net/browse/COLDBOX-1100) Event Caching Does Not Preserve HTTP Response Codes
* [COLDBOX-1099](https://ortussolutions.atlassian.net/browse/COLDBOX-1099) Regression on ColdBox v6.6.1 around usage of statusCode = 0 on relocates
* [COLDBOX-1098](https://ortussolutions.atlassian.net/browse/COLDBOX-1098) RequestService context creation not thread safe
* [COLDBOX-1097](https://ortussolutions.atlassian.net/browse/COLDBOX-1097) Missing scopes on isNull() checks
* [COLDBOX-1092](https://ortussolutions.atlassian.net/browse/COLDBOX-1092) RestHandler Try/Catches Break In Testbox When RunEvent() is Called
* [COLDBOX-1045](https://ortussolutions.atlassian.net/browse/COLDBOX-1045) Scheduled tasks have no default error handling
* [COLDBOX-1043](https://ortussolutions.atlassian.net/browse/COLDBOX-1043) Creating scheduled task with unrecognized timeUnit throws null pointer
* [COLDBOX-1042](https://ortussolutions.atlassian.net/browse/COLDBOX-1042) afterAnyTask() and task.after() don't run after failing task
* [COLDBOX-1040](https://ortussolutions.atlassian.net/browse/COLDBOX-1040) Error in onAnyTaskError() or after() tasks not handled and executor dies.
* [COLDBOX-966](https://ortussolutions.atlassian.net/browse/COLDBOX-966) Coldbox Renderer.RenderLayout() Overwrites Event's Current View

**Improvement**

* [COLDBOX-1124](https://ortussolutions.atlassian.net/browse/COLDBOX-1124) Convert mixer util to script and utilize only the necessary mixins by deprecating older mixins
* [COLDBOX-1116](https://ortussolutions.atlassian.net/browse/COLDBOX-1116) Enhance EntityNotFound Exception Messages for rest handlers
* [COLDBOX-1096](https://ortussolutions.atlassian.net/browse/COLDBOX-1096) SES is always disabled on RequestContext until RoutingService request capture : SES is the new default for ColdBox Apps
* [COLDBOX-1094](https://ortussolutions.atlassian.net/browse/COLDBOX-1094) coldbox 6.5 and 6.6 break ORM event handling in cborm
* [COLDBOX-1067](https://ortussolutions.atlassian.net/browse/COLDBOX-1067) Scheduled Tasks: Inject module context variables to module schedulers and inject global context into global scheduler
* [COLDBOX-1044](https://ortussolutions.atlassian.net/browse/COLDBOX-1044) Create singular aliases for timeunits

**New Feature**

* [COLDBOX-1123](https://ortussolutions.atlassian.net/browse/COLDBOX-1123) New xTask() method in the schedulers that will automatically disable the task but still register it. Great for debugging!
* [COLDBOX-1121](https://ortussolutions.atlassian.net/browse/COLDBOX-1121) Log schedule task failures to console so errors are not ignored
* [COLDBOX-1120](https://ortussolutions.atlassian.net/browse/COLDBOX-1120) Scheduler's onShutdown() callback now receives the boolean force and numeric timeout arguments
* [COLDBOX-1119](https://ortussolutions.atlassian.net/browse/COLDBOX-1119) The Scheduler's shutdown method now has two arguments: boolean force, numeric timeout
* [COLDBOX-1118](https://ortussolutions.atlassian.net/browse/COLDBOX-1118) All schedulers have a new property: shutdownTimeout which defaults to 30 that can be used to control how long to wait for tasks to gracefully complete when shutting down.
* [COLDBOX-1113](https://ortussolutions.atlassian.net/browse/COLDBOX-1113) New coldobx.system.testing.VirtualApp object that can startup,restart and shutdown Virtual Testing Applications
* [COLDBOX-1108](https://ortussolutions.atlassian.net/browse/COLDBOX-1108) Async interceptos can now discover their announced data without duplicating it via cfthread
* [COLDBOX-1107](https://ortussolutions.atlassian.net/browse/COLDBOX-1107) Interception Event pools are now using synchronized linked maps to provide concurrency
* [COLDBOX-1106](https://ortussolutions.atlassian.net/browse/COLDBOX-1106) New super type function "forAttribute" to help us serialize simple/complex data and encoded for usage in html attributes
* [COLDBOX-1101](https://ortussolutions.atlassian.net/browse/COLDBOX-1101) announce `onException` interception from RESTHandler, when exceptions are detected
* [COLDBOX-1053](https://ortussolutions.atlassian.net/browse/COLDBOX-1053) Async schedulers and executors can now have a graceful shutdown and await for task termination with a configurable timeout.
* [COLDBOX-1052](https://ortussolutions.atlassian.net/browse/COLDBOX-1052) Scheduled tasks add start and end date/times

**Task**

* [COLDBOX-1122](https://ortussolutions.atlassian.net/browse/COLDBOX-1122) lucee async tests where being skipped due to missing engine check
* [COLDBOX-1117](https://ortussolutions.atlassian.net/browse/COLDBOX-1117) Remove nextRun stat from scheduled task, it was never implemented
{% endtab %}

{% tab title="CacheBox" %}
**Bug**

* [CACHEBOX-66](https://ortussolutions.atlassian.net/browse/CACHEBOX-66) Cachebox concurrent store meta index not thread safe during reaping

**Improvement**

* [CACHEBOX-82](https://ortussolutions.atlassian.net/browse/CACHEBOX-82) Remove the usage of identity hash codes, they are no longer relevant and can cause contention under load
{% endtab %}

{% tab title="LogBox" %}
**Improvement**

* [LOGBOX-68](https://ortussolutions.atlassian.net/browse/LOGBOX-68) Remove the usage of identity hash codes, they are no longer relevant and can cause contention under load
* [LOGBOX-65](https://ortussolutions.atlassian.net/browse/LOGBOX-65) File Appender missing text "ExtraInfo: "
{% endtab %}

{% tab title="WireBox" %}
**Bug**

* [WIREBOX-126](https://ortussolutions.atlassian.net/browse/WIREBOX-126) Inherited Metadata Usage - Singleton attribute evaluated before Scopes

**Improvement**

* [WIREBOX-129](https://ortussolutions.atlassian.net/browse/WIREBOX-129) Massive refactor to improve object creation and injection wiring
* [WIREBOX-128](https://ortussolutions.atlassian.net/browse/WIREBOX-128) Injector now caches all object contains lookups to increase performance across hierarchy lookups
* [WIREBOX-127](https://ortussolutions.atlassian.net/browse/WIREBOX-127) Lazy load all constructs on the Injector to improve performance
* [WIREBOX-125](https://ortussolutions.atlassian.net/browse/WIREBOX-125) Remove the usage of identity hash codes, they are no longer relevant and can cause contention under load
{% endtab %}
{% endtabs %}
