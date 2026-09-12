# Module Events

| Interception Point | Intercept Structure                        | Description                                                  |
| ------------------ | ------------------------------------------ | ------------------------------------------------------------ |
| preModuleLoad      | {moduleLocation, moduleName}               | This occurs before any module is loaded in the system        |
| postModuleLoad     | {moduleLocation, moduleName, moduleConfig} | This occurs after a module has been loaded in the system     |
| preModuleUnload    | {moduleName}                               | This occurs before a module is unloaded from the system      |
| postModuleUnload   | {moduleName}                               | This occurs after a module has been unloaded from the system |
| preModuleRegistration  | {moduleRegistration, moduleName}       | This occurs right before a single module is registered (before its `ModuleConfig.cfc` is processed) |
| postModuleRegistration | {moduleConfig, moduleName}             | This occurs right after a single module has finished registering (its `ModuleConfig.cfc` has been processed and settings/mappings recorded) |
| afterModuleRegistrations | {moduleRegistry}                     | This occurs once, after **all** discovered modules have finished the registration phase                     |
| afterModuleActivations   | {moduleRegistry}                     | This occurs once, after **all** registered modules have finished the activation phase                       |
