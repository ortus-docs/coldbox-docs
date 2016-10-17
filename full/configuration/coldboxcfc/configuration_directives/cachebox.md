# CacheBox

The CacheBox structure is based on the [CacheBox declaration DSL](http://wiki.coldbox.org/wiki/CacheBox.cfm), and it allows you to customize the caches in your application. Below are the main keys you can fill out, but we recommend you review the CacheBox documentation for further detail.

```js
//cachebox configuration
cachebox = {
    // Location of the configuration CFC for CacheBox
	configFile = "config/CacheBox.cfc",
	// Scope registration for CacheBox
	scopeRegistration = {enabled=true,scope=application,key=cacheBox},
	// Default Cache Configuration
	defaultCache  = "views",
	// Caches Configuration
	caches 	 = {}
};
```

> **Info** : We would recommend you create a `config/CacheBox.cfc` and put all your caching configuration there instead of in the main ColdBox configuration file. This will give you further portability and decoupling.

## ConfigFile
An absolute or relative path to the CacheBox configuration CFC or XML file to use instead of declaring the rest of the keys in this structure. So if you do not define a cacheBox structure, the framework will look for the default value: `config/CacheBox.cfc` and it will load it if found. If not found, it will use the default CacheBox configuration found in `/coldbox/system/web/config/CacheBox.cfc`

## ScopeRegistration
A structure that enables scope registration of the CacheBox factory in either server, cluster, application or session scope.

## DefaultCache
The configuration of the default cache which will have an implicit name of default which is a reserved cache name. It also has a default provider of CacheBox which cannot be changed.

## Caches
A structure where you can create more named caches for usage in your CacheBox factory.