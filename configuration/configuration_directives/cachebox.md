# CacheBox

The CacheBox structure is based on the [CacheBox declaration DSL](http://wiki.coldbox.org/wiki/CacheBox.cfm), and it allows you to customize the caches in your application. Below are the main keys you can fill out, but we recommend you review the CacheBox documentation for further detail.

| key | type | required | default | description |
| -- | -- | -- | -- | -- |
| defaultCache | struct | true | --- | 
| caches | struct | false | `{}` | A structure where you can create more named caches for usage in your CacheBox factory.
| listeners | array	| false	| `[]` | An array that will hold all the listeners you want to configure at startup time for your CacheBox instance. If you are running CacheBox within a ColdBox application, this item is not necessary as you can register them via the main ColdBox interceptors section.

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

## ConfigFile
An absolute or relative path to the CacheBox configuration CFC or XML file to use instead of declaring the rest of the keys in this structure. So if you do not define a cacheBox structure, the framework will look for the default value: `config/CacheBox.cfc` and it will load it if found. If not found, it will use the default CacheBox configuration found in `/coldbox/system/web/config/CacheBox.cfc`

## ScopeRegisration
A structure that enables scope registration of the CacheBox factory in either server, cluster, application or session scope.

## DefaultCache
The configuration of the default cache which will have an implicit name of default which is a reserved cache name. It also has a default provider of CacheBox which cannot be changed.