# Module Plugins


Module plugins are great to build. Not only that but you can easily wire them anywhere in your applications by using the enhanced DSL for plugin autowiring:

* coldbox:myplugin:{name}{@module}

This tells the autowiring DSL to go get a custom plugin with the {name} but located (@) the {module}. Of course, this DSL is only for autowiring or wiring declarations. You can also explicitly get a module plugin by using our favorite plugin method: `getMyPlugin()` or `getPlugin()`:

> **Info**  Please note that all `getMyPlugin()` and `getPlugin()` methods on the ColdBox Factory and ColdBox Remote Proxy have been updated also to reflect the module parameter. 

```js
// Wire in a plugin from the pluginslib module
property name="fileUtils" inject="coldbox:myPlugin:fileUtils@pluginLib";

// Use the fileUtils from the pluginLib module to read a file
contents = getMyPlugin(name="fileUtils",module="pluginLib").readFile( filePath );
```