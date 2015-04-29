# Loading A-la-carte Modules

You can also use the same module service methods to load ANY module in ANY ColdFusion reachable location. This is huge for applications that need granular control of loading and unloading of modules. You will do this with our magic method:

```js
registerAndActivateModule(moduleName,[instantiationpath])
```

This time, you will pass an instantiation path to the method. This is the dot notation path up to the folder that contains the module. So let's say your module to load is located here:

```js
/shared
  /modules
    /Calendar
      +ModuleConfig.cfc
```

You will then issue the following:

```js
controller.getModuleService().registerAndActivateModule('Calendar','shared.modules')
```

> **Important** The instantiation path does NOT include the name of the module on disk as the name is the module name you already pass into the method.

