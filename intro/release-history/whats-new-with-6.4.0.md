# What's New With 6.4.0

ColdBox 6.4.0 is more of a major than a minor release due to the amount of work we have done to bring you one of the most revolutionary features of this framework: **Scheduled Tasks.**  

### ColdBox Scheduled Tasks

Scheduled tasks have always been a point of soreness for many developers in _ANY_ language.  Especially choosing where to place them for execution: should it be cron? windows task scheduler? ColdFusion engine? Jenkins, Gitlab? and the list goes on and on.

![ColdBox Scheduled Tasks](../../.gitbook/assets/coldboxscheduler.png)

The _ColdBox Scheduled Tasks_ offers a fresh, programmatic and human approach to scheduling tasks on your server and multi-server application.  It allows you to define your tasks in a portable **Scheduler** we lovingly call the `Scheduler.cfc` which not only can be used to define your tasks, but also monitor all of their life-cycles and metrics of tasks.  Since ColdBox is also hierarchical, it allows for every single ColdBox Module to also define a `Scheduler` and register their own tasks as well.  This is a revolutionary approach to scheduling tasks in an HMVC application.

You can learn all about them in our two sections:

* [CFML Scheduled Tasks](../../digging-deeper/promises-async-programming/scheduled-tasks.md)
* [ColdBox Scheduled Tasks](../../digging-deeper/scheduled-tasks.md)

### Release Notes

{% tabs %}
{% tab title="ColdBox HMVC" %}
**Bugs**

* [COLDBOX-991](https://ortussolutions.atlassian.net/browse/COLDBOX-991) Fixes issues with Adobe losing App Context in Scheduled Tasks.  You can now run scheduled tasks in Adobe with full app support.
* [COLDBOX-988](https://ortussolutions.atlassian.net/browse/COLDBOX-988) When running scheduled tasks in ACF loading of contexts produce a null pointer exception
* [COLDBOX-981](https://ortussolutions.atlassian.net/browse/COLDBOX-981) `DataMarshaller` no longer accepts 'row', 'column' or 'struct' as a valid argument.

**Improvements**

* [COLDBOX-998](https://ortussolutions.atlassian.net/browse/COLDBOX-998) Convert util to script and optimize
* [COLDBOX-989](https://ortussolutions.atlassian.net/browse/COLDBOX-989) Add more debugging when exceptions occur when loading/unloading thread contexts
* [COLDBOX-971](https://ortussolutions.atlassian.net/browse/COLDBOX-971) Implement caching strategy for application helper lookups into the `default` cache instead of the `template` cache.

**New Features**

* [COLDBOX-999](https://ortussolutions.atlassian.net/browse/COLDBOX-999) New `SchedulerService` that mointors and registers application scheduled tasks in an HMVC fashion
* [COLDBOX-997](https://ortussolutions.atlassian.net/browse/COLDBOX-997) Added out and error stream helpers to Scheduled Tasks for better debugging
* [COLDBOX-996](https://ortussolutions.atlassian.net/browse/COLDBOX-996) `newTask`\(\) method on scheduled executor to replace nameless `newSchedule`
* [COLDBOX-995](https://ortussolutions.atlassian.net/browse/COLDBOX-995) New scheduler object to keep track and metrics of registered tasks
* [COLDBOX-994](https://ortussolutions.atlassian.net/browse/COLDBOX-994) New Scheduled Task with life-cycles and metrics
* [COLDBOX-993](https://ortussolutions.atlassian.net/browse/COLDBOX-993) New async.time package to deal with periods, durations, time offsets and so much more
* [COLDBOX-992](https://ortussolutions.atlassian.net/browse/COLDBOX-992) Added CFML Duration and Periods to async manager so task executions can be nicer and pin point accuracy
* [COLDBOX-990](https://ortussolutions.atlassian.net/browse/COLDBOX-990) Allow structs for query strings when doing relocations
* [COLDBOX-987](https://ortussolutions.atlassian.net/browse/COLDBOX-987) Encapsulate any type of exception in the REST Handler in a `onAnyOtherException`\(\) action which can also be overidden by concrete handlers
* [COLDBOX-986](https://ortussolutions.atlassian.net/browse/COLDBOX-986) Add registration and activation timestamps to the a module configuration object  for active profiling.
* [COLDBOX-985](https://ortussolutions.atlassian.net/browse/COLDBOX-985) Rename `renderLayout()` to just `layout()` and deprecate it for v7
* [COLDBOX-984](https://ortussolutions.atlassian.net/browse/COLDBOX-984) Rename `renderView(`\) to just `view()` and deprecate it for v7
{% endtab %}

{% tab title="WireBox" %}
#### Bugs

* [WIREBOX-112](https://ortussolutions.atlassian.net/browse/WIREBOX-112) virtual inheritance causes double `inits` on objects that do not have a constructor and their parent does.
* [WIREBOX-95](https://ortussolutions.atlassian.net/browse/WIREBOX-95) `onDIComplete`\(\) is called twice using virtual inheritance

#### New Features

* [WIREBOX-114](https://ortussolutions.atlassian.net/browse/WIREBOX-114) New coldbox dsl =&gt; `coldbox:appScheduler` which gives you the `appScheduler@coldbox` instance
* [WIREBOX-113](https://ortussolutions.atlassian.net/browse/WIREBOX-113) new injection dsl: `wirebox:asyncManager`
{% endtab %}
{% endtabs %}

