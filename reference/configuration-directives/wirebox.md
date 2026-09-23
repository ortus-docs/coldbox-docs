---
description: The wirebox configuration directive — how ColdBox wires the WireBox DI container into your application.
icon: plug
---

# WireBox Directive

This configuration structure is used to configure the [WireBox](https://wirebox.ortusbooks.com) dependency injection framework embedded in ColdBox.

📖 **Binder DSL, mapping, scopes, and AOP live in the WireBox book:** [wirebox.ortusbooks.com](https://wirebox.ortusbooks.com) — including the [ColdBox-enhanced binder](https://wirebox.ortusbooks.com/configuration/configuring-wirebox/coldbox-enhanced-binder).

```javascript
// wirebox integration
wirebox = {
    binder = 'config.WireBox',
    singletonReload = true
};
```

**binder**

The location of the WireBox configuration binder to use for the application. If empty, we will use the binder in the `config` folder called by conventions: `WireBox.bx` (or `.cfc` for CFML)

**singletonReload**

A great flag for development. If enabled, WireBox will flush its singleton objects on every request so you can develop without any headaches of reloading.

> **Warning** : This operation can cause some thread issues and it is only meant for development. Use at your own risk.
