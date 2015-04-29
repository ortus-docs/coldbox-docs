# Common Methods

Here are the most common methods you can use to manage modules:

* reloadAll() : Reload all modules in the application. This clears out all module settings, re-registers from disk, re-configures them and activates them
* reload(module) : Target a module reload by name
* unloadAll() : Unload all modules
* unload(module) : Target a module unload by name
* registerAllModules() : Registers all module configurations
* registerModule(moduleName,[instantiationPath]) : Target a module configuration registration
* activateAllModules() : Activate all registered modules
* activateModule(moduleName) : Target activate a module that has been registered already
* getLoadedModules() : Get an array of loaded module names
* rebuildModuleRegistry() : Rescan all the module lcoations for newly installed modules and rebuild the registry so these modules can be registered and activated.
* registerAndActivateModule(moduleName,[instantiationPath]) : Register and Activate a new module

With these methods you can get creative and target the reloading, unloading or loading of specific modules. These methods really open the opportunity to build an abstraction API where you can install modules in your application on the fly and then register and activate them. You can also do the inverse and deactivate modules in a running application.

