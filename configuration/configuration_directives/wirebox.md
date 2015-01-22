# WireBox

This configuration structure is used to configure the [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) dependency injection framework embedded in ColdBox. 

```js
// wirebox integration
wirebox = {
	binder = 'config.WireBox',
	singletonReload = true
};
```

**singletonReload**

A great flag for development. If enabled, on every request WireBox will flush its singleton objects so you can develop without any headaches of reloading.

