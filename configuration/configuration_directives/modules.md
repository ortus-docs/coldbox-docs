# Modules

The modules structure is used to configure the behavior of the [ColdBox Modules](../../modules/index.md).


```js
modules = {
    // Will auto reload the modules in each request. Great for development but can cause some loading/re-loading issues
	autoReload = true,
	include = [],
	exclude = ["paidModule1","paidModule2"]
};
```