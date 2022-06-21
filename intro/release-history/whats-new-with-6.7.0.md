---
description: June 2022
---

# What's New With 6.7.0

## Major Updates

###

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
