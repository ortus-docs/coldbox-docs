---
description: November 18, 2023
---

# What's New With 7.2.0

Welcome to ColdBox 7.2.0, which packs a big punch on stability and tons of new features.

### Release Notes

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
