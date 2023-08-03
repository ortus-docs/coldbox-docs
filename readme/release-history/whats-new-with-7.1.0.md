---
description: August 3, 2023
---

# What's New With 7.1.0

The full release notes per library can be found below. Just click on the library tab and explore their release notes:

{% tabs %}
{% tab title="ColdBox HMVC" %}
**Bug**

* [COLDBOX-1233](https://ortussolutions.atlassian.net/browse/COLDBOX-1233) Exception bean can't cast \`"i"\` to a number value

**New Feature**

* [COLDBOX-1229](https://ortussolutions.atlassian.net/browse/COLDBOX-1229) Added debug argument to ScheduleExecutor and Scheduler when creating tasks for consistency
* [COLDBOX-1230](https://ortussolutions.atlassian.net/browse/COLDBOX-1230) Reorganized ScheduledTasks functions within the CFC into code groups and comments
* [COLDBOX-1235](https://ortussolutions.atlassian.net/browse/COLDBOX-1235) New StringUtil.`prettySQL` method for usage in debugging and whoops reports
* [COLDBOX-1238](https://ortussolutions.atlassian.net/browse/COLDBOX-1238) New testing matcher: `toRedirectTo` for easier testing against relocations
* [COLDBOX-1239](https://ortussolutions.atlassian.net/browse/COLDBOX-1239) New REST convention for custom error handlers: \`on{errorType}Exception()\`

**Improvements**

* [COLDBOX-1041](https://ortussolutions.atlassian.net/browse/COLDBOX-1041) Logging category in ColdBox scheduler is generic
* [COLDBOX-1231](https://ortussolutions.atlassian.net/browse/COLDBOX-1231) Improve RestHandler Exception handler with on#ExceptionType#Exception() convention
* [COLDBOX-1234](https://ortussolutions.atlassian.net/browse/COLDBOX-1234) Account for null or empty incoming json to prettyjson output
* [COLDBOX-1236](https://ortussolutions.atlassian.net/browse/COLDBOX-1236) Incorporate appName into the appHash to give more uniqueness to locks internally

**Tasks**

* [COLDBOX-1237](https://ortussolutions.atlassian.net/browse/COLDBOX-1237) Removal of Lucee RC tests - no longer needed
{% endtab %}

{% tab title="WireBox" %}
**Bug**

* [WIREBOX-148](https://ortussolutions.atlassian.net/browse/WIREBOX-148) Several AOP and Internal WireBox methods not excluded from delegations
* [WIREBOX-150](https://ortussolutions.atlassian.net/browse/WIREBOX-150) Wirebox standalone is missing delegates
* [WIREBOX-151](https://ortussolutions.atlassian.net/browse/WIREBOX-151) Injections are null, sometimes
* [WIREBOX-152](https://ortussolutions.atlassian.net/browse/WIREBOX-152) `getEnv` errors in Binder context
* [WIREBOX-154](https://ortussolutions.atlassian.net/browse/WIREBOX-154) populateFromQuery delegate defaulting composeRelationships to true

**Improvement**

* [WIREBOX-147](https://ortussolutions.atlassian.net/browse/WIREBOX-147) Improve debug logging to not send the full memento on several debug operations

**Task**

* [WIREBOX-149](https://ortussolutions.atlassian.net/browse/WIREBOX-149) \`toWebservice()\` is now deprecated
{% endtab %}
{% endtabs %}
