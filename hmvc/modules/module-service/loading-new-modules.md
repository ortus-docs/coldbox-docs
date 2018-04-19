# Loading New Modules

If you want to load a new module in your application that you have just installed you need to do a series of steps.

1. Drop the module in any of the module locations defined or an a-la-carte location
2. Call `registerAndActivateModule(moduleName,[instantiationPath])` to register and activate the module

```javascript
controller.getModuleService().registerAndActivateModule('TaskManager');
```

