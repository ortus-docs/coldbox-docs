# WireBox

This configuration structure is used to configure the [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) dependency injection framework embedded in ColdBox.

```javascript
// wirebox integration
wirebox = {
    binder = 'config.WireBox',
    singletonReload = true
};
```

**binder**

The location of the WireBox configuration binder to use for the application. If empty, we will use the binder in the `config` folder called by conventions: `WireBox.cfc`

**singletonReload**

A great flag for development. If enabled, WireBox will flush its singleton objects on every request so you can develop without any headaches of reloading.

> **Warning** : This operation can cause some thread issues and it is only meant for development. Use at your own risk.

