# Module Events

|Interception Point|Intercept Structure|Description|
|--|--|--|
|preModuleLoad |{moduleLocation, moduleName} |This occurs before any module is loaded in the system|
|postModuleLoad |{moduleLocation, moduleName, moduleConfig} |This occurs after a module has been loaded in the system|
|preModuleUnload |{moduleName} |This occurs before a module is unloaded from the system|
|postModuleUnload |{moduleName} |This occurs after a module has been unloaded from the system|


