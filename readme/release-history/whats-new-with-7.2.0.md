---
description: November 18, 2023
---

# What's New With 7.2.0

Welcome to ColdBox 7.2.0, which packs a big punch on stability and tons of new features.

## ColdBox Updates

### SchemaInfo Helper

A new helper has been born that assists you with dealing with Database Schema-related methods that are very common and core to ColdBox and supporting modules.  This will grow as needed and be decoupled to its own module later.

* `hasTable()`
* `hasColumn()`
* `getDatabaseInfo()`
* `getTextColumnType()`
* `getDateTimeColumnType()`
* `getQueryParamDateTimeType()`

### Async `allApply()` error handlers

The `allApply()` is great when dealing with async operations on arrays or collections of objects. However, if something blows up, it would blow up with no way for you to log in or know what happened.  However, now you can pass an `errorHandler` argument which is a UDF/closure that will be attached to the `onException()` method of the future object.  This way, you can react, log, or recover.

```cfscript
results = asyncManager.newFuture().allApply(
	items : data,
	fn : ( record ) => {
		var thisItem = new tests.tmp.User();
		thisItem.injectState = variables.injectState;

		// Inject Both States
		thisItem.injectState( protoTypeState );
		thisItem.injectState( record );

		return thisItem;
	},
	errorHandler : ( e ) => systemOutput( "error: #e.message#" )
)
```

### Scheduled Task Groups

All scheduled tasks now have a `group` property so you can group your tasks.  This is now available when creating tasks or setting them manually.

```cfscript
task( "email-notifications" )
  .setGroup( "admin" )
  .call( () => getInstance( "UserService" ).sendNotifications() )
  .everyDayAt( "13:00" )
```

You can then get the group using the `getGroup()` method or it will be added to all task metadata and stats.

### New `everySecond()` period

A new period method shortcut: `everySecond()`.  Very useful so you can fill up your logs with data.

```cfscript
task( "heartbeat" )
    .call( () => systemOutput( "data" ) )
    .everySecond()
```

### Task Results Are Optionals

All task results, if any, are now stored in a ColdBox Optional. Which is a class that can deal with nulls gracefully and it's very fluent:

{% embed url="https://s3.amazonaws.com/apidocs.ortussolutions.com/coldbox-modules/cbproxies/1.1.0/models/Optional.html" %}
API Docs
{% endembed %}

{% hint style="info" %}
A container object which may or may not contain a non-null value. If a value is present, `isPresent()` will return `true` and `get()` will return the value. Additional methods that depend on the presence or absence of a contained value are provided, such as `orElse()` (return a default value if value not present) and `ifPresent()` (execute a block of code if the value is present). See https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html
{% endhint %}

### Task Get Last Result

New method to get the last result, if any, from a task via the `getLastResult()` method.

```cfscript
var result = task.getLastResult().orElse( "nada" )
```

### DateTimeHelper Updates

Lot's of great new methods and goodies so you can deal with date and times and timezones Oh My!

* `now( [timezone] )`
* `getSystemTimezoneAsString()`
* `getLastBusinessDayOfTheMonth()`
* `getFirstBusinessDayOfTheMonth()`
* `dateTimeAdd()`
* `timeUnitToSeconds()`
* `validateTime()`
* `getIsoTime()`
* `toInstant()`
* `toLocalDateTime()`
* `parse()`
* `toLocalDate()`
* `getTimezone()`
* `getSystemTimezone()`
* `toJavaDate()`
* `duration()`
* `period()`

## WireBox Updates

### AOP Auto Mixer

If in your binder you declare aspects or AOP bindings. Then WireBox will automatically detect it and load the AOP Mixer listener for you.  You no longer have to declare it manually.

## CacheBox Updates

### New Struct Literal Config

You can now configure CacheBox by just passing a struct of [CacheBox DSL](https://cachebox.ortusbooks.com/configuration/cachebox-configuration/cachebox-dsl) config data:

[https://cachebox.ortusbooks.com/configuration/cachebox-configuration](https://cachebox.ortusbooks.com/configuration/cachebox-configuration)

```cfscript
new cachebox.system.cache.CacheFactory( {
	// LogBox Configuration file
	logBoxConfig      : "coldbox.system.cache.config.LogBox",
	// Scope registration, automatically register the cachebox factory instance on any CF scope
	// By default it registers itself on server scope
	scopeRegistration : {
		enabled : true,
		scope   : "application", // the cf scope you want
		key     : "cacheBox"
	},
	// The defaultCache has an implicit name of "default" which is a reserved cache name
	// It also has a default provider of cachebox which cannot be changed.
	// All timeouts are in minutes
	// Please note that each object store could have more configuration properties
	defaultCache : {
		objectDefaultTimeout           : 120,
		objectDefaultLastAccessTimeout : 30,
		useLastAccessTimeouts          : true,
		reapFrequency                  : 2,
		freeMemoryPercentageThreshold  : 0,
		evictionPolicy                 : "LRU",
		evictCount                     : 1,
		maxObjects                     : 300,
		objectStore                    : "ConcurrentSoftReferenceStore",
		// This switches the internal provider from normal cacheBox to coldbox enabled cachebox
		coldboxEnabled                 : false
	}
} );
```

## LogBox Updates

### New Struct Literal Config

You can now configure LogBox by just passing a struct of [LogBox DSL](https://logbox.ortusbooks.com/configuration/configuring-logbox/logbox-dsl) config data:

[https://logbox.ortusbooks.com/configuration/configuring-logbox](https://logbox.ortusbooks.com/configuration/configuring-logbox)

```cfscript
var logBox = new logbox.system.logging.LogBox(
   {
	appenders : { myConsoleLiteral : { class : "ConsoleAppender" } },
	root      : { levelmax : "FATAL", appenders : "*" },
	info      : [ "hello.model", "yes.wow.wow" ],
	warn      : [ "hello.model", "yes.wow.wow" ],
	error     : [ "hello.model", "yes.wow.wow" ]
   }
);
```

### Category Appender Excludes

When you declare categories in LogBox you usually choose the appenders to send messages to, but you could never exclude certain ones. Now you can use the `exclude` property:

```javascript
root : { levelmax : "INFO", appenders : "*", exclude: "slackAppender" },
categories = {
    "coldbox.system" = { levelmax="WARN", appenders="*", exclude: "slackAppender" },
    "coldbox.system.web.services.HandlerService" = { levelMax="FATAL", appenders="*", exclude: "slackAppender" },
    "slackLogger" = { levelmax="WARN", appenders="slackAppender", exclude: "slackAppender" }
}
```

### New Event Listeners

You now have two new event listeners that all LogBox appenders can listen to:

* `preProcessQueue( queue, context )` : Fires before a log queue is processed element by element.
* `postProcessQueue( queue, context )` : After the log queue has been processed and after the listener has slept.

### `processQueueElement` receives the queue

The `processQueueElement( data, context, queue )` now receives the entire queue as well as the `queue` third argument.

### New Archive Layouts

If you use the `RollingFileAppender` the default layout format of the archive package was static and you could not change it.  The default is:

```cfscript
#filename#-#yyy-mm-dd#-#hh-mm#-#archiveNumber#
```

Now you have the power. You can set a property for the appender called `archiveLayout` which maps to a closure/UDF that will build out the layout of the file name.

```cfscript

appenders : {
  files : {
      class : "RollingFileAppender",
      properties : {
          archiveLayout : variables.getDefaultArchiveLayout
      }
  }
}

function getDefaultArchiveLayout( required filename, required archiveCount ){
    return arguments.fileName &
    "-" &
    dateFormat( now(), "yyyy-mm-dd" ) &
    "-" &
    timeFormat( now(), "HH-mm" ) &
    "-#arguments.archiveCount + 1#";
}
```

## Release Notes

The full release notes per library can be found below. Just click on the library tab and explore their release notes:

{% tabs %}
{% tab title="ColdBox HMVC" %}
#### New Feature

[COLDBOX-1248](https://ortussolutions.atlassian.net/browse/COLDBOX-1248) Scheduled tasks now get a \`group\` property so you can use it for grouping purposes

[COLDBOX-1252](https://ortussolutions.atlassian.net/browse/COLDBOX-1252) New \`now()\` method in the DateTmeHelper with optional TimeZone

[COLDBOX-1253](https://ortussolutions.atlassian.net/browse/COLDBOX-1253) New datetimehelper method: getSystemTimezoneAsString()

[COLDBOX-1256](https://ortussolutions.atlassian.net/browse/COLDBOX-1256) New ScheduledTask helper: getLastResult() to get the latest result

[COLDBOX-1257](https://ortussolutions.atlassian.net/browse/COLDBOX-1257) LastResult is now a cbproxies Optional to denote a value or not (COMPAT)

[COLDBOX-1258](https://ortussolutions.atlassian.net/browse/COLDBOX-1258) new scheduledTask method: isEnabled() to verify if the task is enabled

[COLDBOX-1259](https://ortussolutions.atlassian.net/browse/COLDBOX-1259) Complete rewrite of Scheduled Task setNextRuntime() calculations to account for start end running scenarios

[COLDBOX-1260](https://ortussolutions.atlassian.net/browse/COLDBOX-1260) new ScheduledTask period : everySecond()

[COLDBOX-1262](https://ortussolutions.atlassian.net/browse/COLDBOX-1262) New SchemaInfo helper to help interrogate databases for metadata

[COLDBOX-1263](https://ortussolutions.atlassian.net/browse/COLDBOX-1263) Add an errorHandler to the allApply method so you can attach your own error handler to each future computation

#### Improvement

[COLDBOX-1246](https://ortussolutions.atlassian.net/browse/COLDBOX-1246) casting to long instead of int when using LocalDateTime and plus methods to avoid casting issues.

[COLDBOX-1247](https://ortussolutions.atlassian.net/browse/COLDBOX-1247) Do not expose restful handler exception data unless you are in debug mode

[COLDBOX-1250](https://ortussolutions.atlassian.net/browse/COLDBOX-1250) RestHandler.cfc should catch NotAuthorized exception

[COLDBOX-1254](https://ortussolutions.atlassian.net/browse/COLDBOX-1254) getFirstBusinessDayOfTheMonth(), getLastBusinessDayOfTheMonth() now refactored to the dateTimeHelper

[COLDBOX-1255](https://ortussolutions.atlassian.net/browse/COLDBOX-1255) validateTime() is now a helper method in the DateTimeHelper

[COLDBOX-1261](https://ortussolutions.atlassian.net/browse/COLDBOX-1261) Migration of old tasks to new task syntax of task()

#### Bug

[COLDBOX-1241](https://ortussolutions.atlassian.net/browse/COLDBOX-1241) Scheduled Task Stats "NextRun", "Created", "LastRun" Using Wrong Timezones

[COLDBOX-1244](https://ortussolutions.atlassian.net/browse/COLDBOX-1244) onSessionEnd Error when using Coldbox\_App\_Key

[COLDBOX-1245](https://ortussolutions.atlassian.net/browse/COLDBOX-1245) Scheduled task isConstrainted() on day of the month was calculating the days in month backwards

[COLDBOX-1251](https://ortussolutions.atlassian.net/browse/COLDBOX-1251) set next run time when using first or last business day was not accounting times
{% endtab %}

{% tab title="WireBox" %}
### New Feature

[WIREBOX-61](https://ortussolutions.atlassian.net/browse/WIREBOX-61) Make `wirebox.system.aop.Mixer` listener load automatically if any aspects are defined/mapped
{% endtab %}

{% tab title="CacheBox" %}
### Improvement

[CACHEBOX-70](https://ortussolutions.atlassian.net/browse/CACHEBOX-70) Support ad-hoc struct literal of CacheBox DSL to configure CacheBox
{% endtab %}

{% tab title="LogBox" %}
### New Feature

[LOGBOX-75](https://ortussolutions.atlassian.net/browse/LOGBOX-75) New listeners for all appenders: preProcessQueue() postProcessQueue()

[LOGBOX-76](https://ortussolutions.atlassian.net/browse/LOGBOX-76) Add the queue as an argument to the processQueueElement() method

[LOGBOX-79](https://ortussolutions.atlassian.net/browse/LOGBOX-79) new rolling appender property `archiveLayout` which is a closure that returns the pattern of the archive layout

### Bug

[LOGBOX-73](https://ortussolutions.atlassian.net/browse/LOGBOX-73) Unhandled race conditions in FileRotator lead to errors and potential log data loss

[LOGBOX-77](https://ortussolutions.atlassian.net/browse/LOGBOX-77) log rotator was not checking for file existence and 1000s of errors could be produced

### Improvement

[LOGBOX-62](https://ortussolutions.atlassian.net/browse/LOGBOX-62) Support ad-hoc struct literal of LogBox DSL to configure LogBox

[LOGBOX-70](https://ortussolutions.atlassian.net/browse/LOGBOX-70) Add \`Exclude\` key to Logbox Categories to Easily Exclude Appenders

[LOGBOX-74](https://ortussolutions.atlassian.net/browse/LOGBOX-74) shutdown the appenders first instead of the executors to avoid chicken and egg issues

[LOGBOX-78](https://ortussolutions.atlassian.net/browse/LOGBOX-78) Change fileMaxArchives default from 2 to 10

### Task

[LOGBOX-72](https://ortussolutions.atlassian.net/browse/LOGBOX-72) Removal of instance approach in preferences to accessors for the LogBoxConfig
{% endtab %}
{% endtabs %}
