# Modules

The modules structure is used to configure the behavior of the [ColdBox Modules](../../../modules/index.md).


```js
modules = {
    // Will auto reload the modules in each request. Great for development but can cause some loading/re-loading issues
	autoReload = true,
	// An array of modules to load ONLY
	include = [],
	// An array of modules to EXCLUDE for operation
	exclude = ["paidModule1","paidModule2"]
};
```

> **Danger** Please be very careful when using the `autoReload` flag as module routing can be impaird and thread consistency will also suffer. This is PURELY a development flag that you can use at your own risk.