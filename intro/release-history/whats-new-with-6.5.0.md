---
description: 'July 9th, 2021'
---

# What's New With 6.5.x

## Compatibility Notes

Please note that the following ticket corrects behavior in ColdBox that MIGHT affect `interceptors` that have injected dependencies that have the same name as application helper methods from other modules. 

Example:

```javascript
component 
{

    // inject cbauth so we can use it in our interceptor
    property name="auth" inject="provider:authenticationService@cbauth";

    function preProcess( event ) {

        writeDump( auth.isLoggedIn() );

    }

}
```

The interceptor above has a dependency of `auth` from the `cbauth` module.  However, the `cbauth` module also has an application helper called `auth`. So at runtime, this will throw an exception:

```text
Routines cannot be declared more than once.
The routine auth has been declared twice in different templates.

ColdFusion cannot determine the line of the template that caused this error. This is often caused by an error in the exception handling subsystem.
```

This is because now we can't inject the `cbauth` mixin because we already have a `cbauth` dependency injected.  **The resolution, is to RENAME the injection variables so they don't collide with module application helpers.**

## 6.5.2 Release Notes - July 14, 2021

**Regression**

* [COLDBOX-1024](https://ortussolutions.atlassian.net/browse/COLDBOX-1024) Module helpers no longer injected/mixed into interceptors

## 6.5.1 Release Notes - July 12th, 2021

#### Bug

* [COLDBOX-1024](https://ortussolutions.atlassian.net/browse/COLDBOX-1024) Module helpers no longer injected/mixed into interceptors

#### Improvement

* [COLDBOX-1026](https://ortussolutions.atlassian.net/browse/COLDBOX-1026) Update `BeanPopulator` for Hibernate 5 detection in Lucee new extension
* [COLDBOX-1025](https://ortussolutions.atlassian.net/browse/COLDBOX-1025) Added back the finally block, just to make sure cleanup are done in bootstrap reinits

## 6.5.0 Release Notes - July 9th, 2021

{% tabs %}
{% tab title="ColdBox HMVC" %}
#### Bugs

[COLDBOX-1021](https://ortussolutions.atlassian.net/browse/COLDBOX-1021) fix `lastBusinessDay` tests to reflect if the now is the actual last business day of the month

[COLDBOX-1020](https://ortussolutions.atlassian.net/browse/COLDBOX-1020) ColdBox does not allow for non-existent client cookies when using Flash RAM

[COLDBOX-1019](https://ortussolutions.atlassian.net/browse/COLDBOX-1019) ACF 2021 introduced `getTimezone()` so we need to be specific when getting timezones in the scheduler or else it fails

[COLDBOX-1016](https://ortussolutions.atlassian.net/browse/COLDBOX-1016) CF-2018 `stats.lastResult` is null when task-&gt;call have `runEvent("xxxx")`

[COLDBOX-1015](https://ortussolutions.atlassian.net/browse/COLDBOX-1015) Element cleaner not matching on query strings due to misordering of map keys. Use a `TreepMap` to normalize ordering so event caching and view caching cleanups can ocur.

[COLDBOX-1014](https://ortussolutions.atlassian.net/browse/COLDBOX-1014) response object cached response was never used, rely on the master bootstrap ColdBox cache response header

[COLDBOX-1013](https://ortussolutions.atlassian.net/browse/COLDBOX-1013) `RendererEncapsulator` overwrites `view()`-Method from `FrameworkSupertype`

[COLDBOX-1010](https://ortussolutions.atlassian.net/browse/COLDBOX-1010) `this.nullSupport = true` breaks coldbox

[COLDBOX-1008](https://ortussolutions.atlassian.net/browse/COLDBOX-1008) `ScheduleTask` listeners are only testing for `isClosure()` and lambdas return false, so do an or check for `isCustomFunction() to` support lambdas

[COLDBOX-1007](https://ortussolutions.atlassian.net/browse/COLDBOX-1007) scheduled task doesn't listen to `when()` in `Scheduler.cfc`

[COLDBOX-1004](https://ortussolutions.atlassian.net/browse/COLDBOX-1004) Custom matchers `debug()` function not found since it's injected in isolation

[COLDBOX-1003](https://ortussolutions.atlassian.net/browse/COLDBOX-1003) Scheduler service DI constructor argument was using the string literal instead of the string value for the scheduler name

[COLDBOX-1002](https://ortussolutions.atlassian.net/browse/COLDBOX-1002) added this scope to `getTimezone()` to address issue with ACF-2021 bif

[COLDBOX-1001](https://ortussolutions.atlassian.net/browse/COLDBOX-1001) category not logged correctly in async loggers

#### Improvements

[COLDBOX-1023](https://ortussolutions.atlassian.net/browse/COLDBOX-1023) Readjustments to fail fast and reloading procedures when there are exceptions and reiniting becomes impossible.

[COLDBOX-1018](https://ortussolutions.atlassian.net/browse/COLDBOX-1018) Enable proxy to use the ColdBox app key defined in the bootstrap instead of hard coding it

[COLDBOX-1017](https://ortussolutions.atlassian.net/browse/COLDBOX-1017) Delay `loadApplicationHelpers()` in interceptors so modules can contribute udf helpers and even core interceptors can load them.

[COLDBOX-1012](https://ortussolutions.atlassian.net/browse/COLDBOX-1012) Move coldbox inited flag to after after aspects load, so we can make sure all modules are loaded before serving requests

[COLDBOX-1009](https://ortussolutions.atlassian.net/browse/COLDBOX-1009) RESTHandler capture JWT TokenException to authentication failures
{% endtab %}

{% tab title="CacheBox" %}
#### Bugs

[CACHEBOX-68](https://ortussolutions.atlassian.net/browse/CACHEBOX-68) BlackHoleStore never finishes reap\(\) method
{% endtab %}

{% tab title="LogBox" %}
#### Improvements

[LOGBOX-63](https://ortussolutions.atlassian.net/browse/LOGBOX-63) Allow for dbappender to have default column maps instead of strict maps and allow for all methods to use the maps

#### New Features

[LOGBOX-64](https://ortussolutions.atlassian.net/browse/LOGBOX-64) Ability to add new appenders after config has been registered already
{% endtab %}
{% endtabs %}

