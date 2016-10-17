# Module Lifecycle

![](../../images/ModulesLifecycle.jpg)

However, before we start reviewing the module service methods let's review how modules get loaded in a ColdBox application. Below is a simple bullet point of what happens in your application when it starts up:

1. ColdBox main application and configuration loads
2. ColdBox Cache, Logging and WireBox are created
3. Module Service calls on `registerAllModules()` to read all the modules in the modules locations (with include/excludes) and start registering their configurations one by one. If the module had parent settings, interception points, datasoures or webservices, these are registered here.
4. All main application interceptors are loaded and configured
5. ColdBox is marked as initialized
6. Module service calls on `activateAllModules()` so it begins activating only the registered modules one by one. This registers the module's SES URL Mappings, model objects, etc
7. `afterConfigurationLoad` interceptors are fired

