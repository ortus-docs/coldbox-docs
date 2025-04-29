---
description: April 27, 2025
---

# What's New With 7.4.0

Welcome to ColdBox 7.4.0, our last planned release for the 7.x series before our 8.x series starts.  Let's review the major updates of these release.



## ColdBox Updates

<figure><img src="../../.gitbook/assets/logo-gradient-dark.png" alt=""><figcaption><p>www.boxlang.io</p></figcaption></figure>

### BoxLang Support

With the latest updates, ColdBox extends its capabilities to support the development of entire applications using BoxLang exclusively. This enhancement allows developers to leverage the full power and modern syntax of BoxLang, streamlining the development process and adopting current best practices.&#x20;

For those who prefer a gradual transition, ColdBox offers the flexibility to integrate BoxLang within existing ColdFusion Markup Language (CFML) applications. Consequently, developers can choose to write components, handlers, views, and models using either BoxLang, CFML, or a combination of both. This dual-support approach not only facilitates a seamless transition for applications migrating from CFML to BoxLang but also enables new projects to be crafted entirely in the more modern and robust BoxLang environment.

Nothing for you to do except create `.bx, bxm` templates for your application

### BoxLang Template

You also now have a `bx-default` application template you can use to build your applications with.  You can find it here: [https://github.com/coldbox-templates/bx-default](https://github.com/coldbox-templates/bx-default)

## CacheBox Updates

### DiskStore Rewrite

The entire `DiskStore` has been rewritten to include better performance but more importantly to be able to be used concurrently and on distributed network drives. It no longer relies on in-memory indexes and each store on each cluster node can rebuild itself.

### BoxLang Providers

If you are migrating your ColdBox applications then you will need to update your Lucee/CF Providers to use the BoxLang ones:

| Lucee                | Adobe             | BoxLang Equivalent     |
| -------------------- | ----------------- | ---------------------- |
| LuceeProvider        | CFProvider        | BoxLangProvider        |
| LuceeColdboxProvider | CFColdBoxProvider | BoxLangColdBoxProvider |

### Memory Indexers

Memory indexers have also been rewritten to improve stability on clustered servers and to avoid null issues and double map tracking.



## LogBox Upates

Rollling file threads had an bad setting and they would be running on a 1 millisecond timer.  This not only has been mitigated, but also made it configurable via the `rotatorSchedulerMinutes` property on the appender.



## WireBox Updates

WireBox now completely talks BoxLang and CMFL.  Giving it the ability to inject, build and compose objects of either language.



## Release Notes

{% tabs %}
{% tab title="ColdBox HMVC" %}
### Improvement

[COLDBOX-1286](https://ortussolutions.atlassian.net/browse/COLDBOX-1286) Make sure global scheduler is loaded after modules load.

[COLDBOX-1287](https://ortussolutions.atlassian.net/browse/COLDBOX-1287) If running scheduled tasks manually with a \`force\` then do not set the next run since this is a forced run.

[COLDBOX-1288](https://ortussolutions.atlassian.net/browse/COLDBOX-1288) Allow Disabling of PrettyJson in Log Output

[COLDBOX-1294](https://ortussolutions.atlassian.net/browse/COLDBOX-1294) Call \`reset()\` when testing and unloadColdbox is true in the \`afterTests()\` to make sure the reset procedures are done, not only the shutdown.

[COLDBOX-1305](https://ortussolutions.atlassian.net/browse/COLDBOX-1305) Improvement: Update Config Seeding to Include Original Module Settings

#### New Feature

[COLDBOX-1285](https://ortussolutions.atlassian.net/browse/COLDBOX-1285) BoxLang Support so you can write your ColdBox apps in BoxLang

[COLDBOX-1296](https://ortussolutions.atlassian.net/browse/COLDBOX-1296) New flow peek() method to do fluent peeks in objects

[COLDBOX-1297](https://ortussolutions.atlassian.net/browse/COLDBOX-1297) Schedulers now have a startupTask( task ) to dynamically startup a task by name or task object

[COLDBOX-1306](https://ortussolutions.atlassian.net/browse/COLDBOX-1306) Better tracking of status code and text on the request context so integration tests are not corrupted by response commitment: getStatusCode(), getStatusText() accessors

#### Bug

[COLDBOX-1280](https://ortussolutions.atlassian.net/browse/COLDBOX-1280) coldbox.system.cache.policies.LRU cleanup errors

[COLDBOX-1281](https://ortussolutions.atlassian.net/browse/COLDBOX-1281) DateTimeHelper.validateTime Regression

[COLDBOX-1282](https://ortussolutions.atlassian.net/browse/COLDBOX-1282) ScheduledTasks Bug - java.util.concurrent.TimeUnit type error

[COLDBOX-1289](https://ortussolutions.atlassian.net/browse/COLDBOX-1289) Directory Helper Logic in Renderer.cfc is Incorrect and Generates Bad Path On Windows

[COLDBOX-1295](https://ortussolutions.atlassian.net/browse/COLDBOX-1295) Dumps in BugReport.cfm need top attributes

[COLDBOX-1304](https://ortussolutions.atlassian.net/browse/COLDBOX-1304) thisLocationToken is not var scoped

[COLDBOX-1311](https://ortussolutions.atlassian.net/browse/COLDBOX-1311) Remove direct calls to the servlet response.setStatus( int, String ) methods due to jakarta removing statusText

[COLDBOX-1317](https://ortussolutions.atlassian.net/browse/COLDBOX-1317) ACF 2025 has removed htmlEditFormat

[COLDBOX-1326](https://ortussolutions.atlassian.net/browse/COLDBOX-1326) populator discover entity name not accouting for missing entityname

[COLDBOX-1327](https://ortussolutions.atlassian.net/browse/COLDBOX-1327) \`layoutLocations\` variable error when using event.noLayout()
{% endtab %}

{% tab title="CacheBox" %}
### New Feature

[CACHEBOX-87](https://ortussolutions.atlassian.net/browse/CACHEBOX-87) BoxLang Providers

[CACHEBOX-88](https://ortussolutions.atlassian.net/browse/CACHEBOX-88) New approach for indexing objects on object stores to allow for concurrency and without separating indexers

### Bug

[CACHEBOX-86](https://ortussolutions.atlassian.net/browse/CACHEBOX-86) JDBCStore has double now overrides when doing insert/updates on caching. Remove one of them

### Improvement

[CACHEBOX-42](https://ortussolutions.atlassian.net/browse/CACHEBOX-42) Rewrite CacheBox DiskStore to not rely on in-memory indexer
{% endtab %}

{% tab title="LogBox" %}
### Bug

[LOGBOX-81](https://ortussolutions.atlassian.net/browse/LOGBOX-81) Make the Rolling File Appender rotator task timer configurable: rotatorSchedulerMinutes property

[LOGBOX-82](https://ortussolutions.atlassian.net/browse/LOGBOX-82) LogBox Rolling file threads rotator running on an insane millisecond timer instead of minutes
{% endtab %}

{% tab title="WireBox" %}
### Bug

[WIREBOX-155](https://ortussolutions.atlassian.net/browse/WIREBOX-155) ExceptionBean is referenced but not shipped with Standalone install
{% endtab %}
{% endtabs %}
