# Module Dependencies

Modules can declare other module dependencies in the `ModuleConfig.cfc` via the `this.dependencies` property. This means that **before** the declared module is activated, the dependencies will be registered and activated **FIRST** and then the declared module will load.

```javascript
this.dependencies = [ "javaloader" ];
```

This means that the `javaloader` module HAS to load and activate first before this module can finish registering and activating.

