# What's New With 5.4.0

ColdBox 5.4.0 is a minor version update with lots of fixes, improvements, performance enhancements and some nice new features.  Below are the major areas of improvement and the full release notes. To update your ColdBox installation just leverage CommandBox: 

* `update coldbox`
* `update logbox`
* `update wirebox`
* `update cachebox`

## Box Namespace

In our initiative to make all Modules sharable between ColdBox, CommandBox and whatever other boxes we make in the future.  We created the `box` alias for injections. In our case any injection with the prefix `coldbox` can be used as `box`

## RunRoute\(\)

We have created a new internal runner called `runRoute()` which is similar to `runEvent()` but it allows you to abstract your events by leveraging named routes.  So just like you can create links based on named routes and params, you can execute named routes and params as well internally via `runRoute()`

```javascript
/**
 * Executes internal named routes with or without parameters. If the named route is not found or the route has no event to execute then this method will throw an `InvalidArgumentException`.
 * If you need a route from a module then append the module address: `@moduleName` or prefix it like in run event calls `moduleName:routeName` in order to find the right route.
 * The route params will be passed to events as action arguments much how eventArguments work.
 *
 * @name The name of the route
 * @params The parameters of the route to replace
 * @cache Cached the output of the runnable execution, defaults to false. A unique key will be created according to event string + arguments.
 * @cacheTimeout The time in minutes to cache the results
 * @cacheLastAccessTimeout The time in minutes the results will be removed from cache if idle or requested
 * @cacheSuffix The suffix to add into the cache entry for this event rendering
 * @cacheProvider The provider to cache this event rendering in, defaults to 'template'
 * @prePostExempt If true, pre/post handlers will not be fired. Defaults to false
 *
 * @throws InvalidArgumentException
 */
any function runRoute(
	required name,
	struct params={},
	boolean cache=false,
	cacheTimeout="",
	cacheLastAccessTimeout="",
	cacheSuffix="",
	cacheProvider="template",
	boolean prePostExempt=false
)
```

## CacheBox Rewritten

This should have been a major release on its own.  The entire CacheBox framework was re-written in script and modernized from top to bottom.  We removed all implicit variable access which gave us huge performance boosts and we streamlined all operations with modern techniques.  The results are great and our maintenance will be much less in the future. A part from those optimizations we managed to add a few nice items:

* Adobe 2018 certified
* New setting `resetTimeoutOnAccess` which allows you to simulate session scopes on any CacheBox cache.  Every time a `get()` operation is done, that item's timeout will be reset.
* All cache providers get some multi function goodness: `setMulti(), getMulti(), lookupMulti(), clearMulti(),getCachedObjectMetadataMulti()`

### LogBox Improvements

There are two major improvements we did with LogBox in this release:

1\) The file locking operations on file appenders have been streamlined to avoid high i/o operations.

2\) The console appender uses an asynchronous streaming technique which makes it extremely efficient and fast.

## ColdBox Release Notes

### Bugs

* \[[COLDBOX-556](https://ortussolutions.atlassian.net/browse/COLDBOX-556)\] - `prePostExempt` doesn't skip around advices
* \[[COLDBOX-755](https://ortussolutions.atlassian.net/browse/COLDBOX-755)\] - Core interceptors in coldbox.cfc do not listen or register custom interception points that are contributed by modules
* \[[COLDBOX-761](https://ortussolutions.atlassian.net/browse/COLDBOX-761)\] - `invalidEventHandler` gets in to an infinite loop when the `invalidEventHandler` isn't a full event
* \[[COLDBOX-766](https://ortussolutions.atlassian.net/browse/COLDBOX-766)\] - ColdBox shutdown errors `onApplicationEnd` due to lack of application scope
* \[[COLDBOX-770](https://ortussolutions.atlassian.net/browse/COLDBOX-770)\] - Use of `event.sendFile` delivers a file with single quotes in the name
* \[[COLDBOX-773](https://ortussolutions.atlassian.net/browse/COLDBOX-773)\] - Remove default defaultValue as it never will throw an exception if missing on requestcontext on `getHTTPHeader()`

### New Features

* \[[COLDBOX-765](https://ortussolutions.atlassian.net/browse/COLDBOX-765)\] - Update all elixir methods to match new version
* \[[COLDBOX-771](https://ortussolutions.atlassian.net/browse/COLDBOX-771)\] - Add Elixir version 3 path methods
* \[[COLDBOX-775](https://ortussolutions.atlassian.net/browse/COLDBOX-775)\] - Added ```:``` as a delimiter for the `route()` method when using modules to be consistent with run event
* \[[COLDBOX-776](https://ortussolutions.atlassian.net/browse/COLDBOX-776)\] - new runner: `runRoute()` that allows you to run routes internally with param passing

### Improvements

* \[[COLDBOX-767](https://ortussolutions.atlassian.net/browse/COLDBOX-767)\] - Change "module already registered" from warn to debug
* \[[COLDBOX-774](https://ortussolutions.atlassian.net/browse/COLDBOX-774)\] - Introduce generic "**box**" namespace for Wirebox injections

## CacheBox Release Notes

### Bugs

* \[[CACHEBOX-46](https://ortussolutions.atlassian.net/browse/CACHEBOX-46)\] - CacheboxProvider metadata and stores: use CFML functions on java hash maps breaks concurrency
* \[[CACHEBOX-50](https://ortussolutions.atlassian.net/browse/CACHEBOX-50)\] - getOrSet\(\) provider method doesn't work with full null support
* \[[CACHEBOX-52](https://ortussolutions.atlassian.net/browse/CACHEBOX-52)\] - getQuiet\(\), clearQuiet\(\), getSize\(\), clearAll\(\), expireAll\(\) broken in acf providers

### New Features

* \[[CACHEBOX-48](https://ortussolutions.atlassian.net/browse/CACHEBOX-48)\] - New setting: \`resetTimeoutOnAccess\` to allow the ability to reset timeout access for entries
* \[[CACHEBOX-49](https://ortussolutions.atlassian.net/browse/CACHEBOX-49)\] - Global script conversion and code optimizations
* \[[CACHEBOX-53](https://ortussolutions.atlassian.net/browse/CACHEBOX-53)\] - lookup operations on ACF providers updated to leverage cacheIdExists\(\) improves operation by over 50x
* \[[CACHEBOX-54](https://ortussolutions.atlassian.net/browse/CACHEBOX-54)\] - setMulti\(\), getMulti\(\), lookupMulti\(\), clearMulti\(\),getCachedObjectMetadataMulti\(\) is now available on all cache providers

### Improvements

* \[[CACHEBOX-47](https://ortussolutions.atlassian.net/browse/CACHEBOX-47)\] - Increased timeout on \`getOrSet\` lock
* \[[CACHEBOX-51](https://ortussolutions.atlassian.net/browse/CACHEBOX-51)\] - Consolidated usages of the abstract cache provider to all providers to avoid redundancy

## WireBox Release Notes

### Bugs

* \[[WIREBOX-82](https://ortussolutions.atlassian.net/browse/WIREBOX-82)\] - `builder.toVirtualInheritance()`: scoping issues
* \[[WIREBOX-83](https://ortussolutions.atlassian.net/browse/WIREBOX-83)\] - When using sandbox security, and using a provider DSL the file existence checks blow up

## LogBox Release Notes

### New Features

* \[[LOGBOX-34](https://ortussolutions.atlassian.net/browse/LOGBOX-34)\] - Console appender completely rewritten to support asynchronous streaming

### Improvements

* \[[LOGBOX-33](https://ortussolutions.atlassian.net/browse/LOGBOX-33)\] - Improve file exists usage on file appenders to avoid i/o operations

