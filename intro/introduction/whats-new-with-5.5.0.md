# What's New With 5.5.0

ColdBox 5.5.0 is a minor version update with lots of fixes, improvements, performance enhancements and some nice new features.  Below are the major areas of improvement and the full release notes. To update your ColdBox installation just leverage CommandBox: 

* `update coldbox`
* `update logbox`
* `update wirebox`
* `update cachebox`

The major libraries updated in this release are ColdBox MVC and WireBox.

## Compatibility Notes

If you are using annotations for component **aliases** you will have to tell WireBox explicitly to process those mappings.  As by default, we no longer process mappings on startup.

```javascript
// Process a single mapping immediately
map( name ).process();

// Process all mappings in the mapDiretory() call
mapDirectory( packagePath="my.path", process=true )
mapDirectory( "my.path" ).process();

// Map all models in a module via the this.autoProcessModels in the ModuleConfig.cfc
this.autoProcessModels = true

```

## Major Updates

### Performance

This release is a big performance boost for two areas of operation: **modules, and models**.  The Module Service has been completely revised to make it as fast as possible when registering and activating modules.  If you have an extensive usage of modules, you will feel the difference when booting up or re-initing the framework.

The other area is the inspection of models that has been lazy evaluated.  This allows for faster bootups and reinits as models are only inspected on demand or when explicitly marked.

### More Environment Detection

Our environment functions have now been added to the Framework SuperType and thus all objects in the ColdBox eco-system receive it:

* `getEnv(), getSystemSetting() and getSystemProperty()`

### Custom Resource Patterns

As resources have become more mainstream in ColdBox, we now give you the ability to choose the URL pattern you want to attach to the resource.  This allows you to create a-la-carte resource patterns and also allow you to nest resources via patterns.

```javascript
resources(
  pattern="/site/:siteId/agents",
  resource="agents"
);
```

## ColdBox Release Notes

### Bugs

* \[[COLDBOX-786](https://ortussolutions.atlassian.net/browse/COLDBOX-786)\] - HTMLHelper typo on `getSetting` call via `elixirpath()`
* \[[COLDBOX-788](https://ortussolutions.atlassian.net/browse/COLDBOX-788)\] - Private method in handler is accessible from public \( direct link \)

### New Features

* \[[COLDBOX-783](https://ortussolutions.atlassian.net/browse/COLDBOX-783)\] - New module directive: `autoProcessModels` which defaults to **false** to defer to lazy processing of models
* \[[COLDBOX-789](https://ortussolutions.atlassian.net/browse/COLDBOX-789)\] - Added `getEnv(), getSystemSetting() and getSystemProperty()` to super type for easier environment setting retrievals
* \[[COLDBOX-790](https://ortussolutions.atlassian.net/browse/COLDBOX-790)\] - Added much more logging and info when booting up the Module Service
* \[[COLDBOX-791](https://ortussolutions.atlassian.net/browse/COLDBOX-791)\] - `buildLink(), relocate()` now will translate raw `: to / in URL` with appropriate module entry points thanks to richard herbert
* \[[COLDBOX-792](https://ortussolutions.atlassian.net/browse/COLDBOX-792)\] - Allow nested resources and the ability for custom URL patterns for resources

### Improvements

* \[[COLDBOX-782](https://ortussolutions.atlassian.net/browse/COLDBOX-782)\] - Add TestBox 3 and code coverage to the core
* \[[COLDBOX-785](https://ortussolutions.atlassian.net/browse/COLDBOX-785)\] - Module service performance optimizations for module activations.
* \[[COLDBOX-787](https://ortussolutions.atlassian.net/browse/COLDBOX-787)\] - Update RequestContext.cfc `getFullUrl()` to include port number

## WireBox Release Notes

### New Features

* \[[WIREBOX-84](https://ortussolutions.atlassian.net/browse/WIREBOX-84)\] - Remove auto processing of all mappings defer to lazy loading
* \[[WIREBOX-85](https://ortussolutions.atlassian.net/browse/WIREBOX-85)\] - `MapDirectory` new boolean argument `process` which defers to **false**, if **true**, the mappings will be auto processed
* \[[WIREBOX-86](https://ortussolutions.atlassian.net/browse/WIREBOX-86)\] - New binder method: `process()` if chained from a mapping, it will process the mapping's metadata automatically.

### Improvement

* \[[WIREBOX-87](https://ortussolutions.atlassian.net/browse/WIREBOX-87)\] - AOP debug logging as serialized CFCs which clogs log files

