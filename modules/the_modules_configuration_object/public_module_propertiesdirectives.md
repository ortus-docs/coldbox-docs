# Public Module Properties/Directives

First of all, we must define some public properties that can easily identify or provide directives for the module:


|Property|Type|Required|Default|Description|
|--|--|--|--|--|
|title|string|true|---|The title of the module|
|author|string|true|---|The author of the module|
|webURL|string|true|---|The web URL associated with this module. Maybe for documentation, blog, links, etc.|
|description|string|true|---|A short description about the module |
|version|string|true|---|The version of the module|
|viewParentLookup |boolean|false|true|If true, coldbox checks for views in the parent overrides first, then in the module. If false, coldbox checks for views in the module first, then the parent.|
|layoutParentLookup |boolean|false|true|If true, coldbox checks for layouts in the parent overrides first, then in the module. If false, coldbox checks for layouts in the module first, then the parent. |
|entryPoint|event or route|false|---|This is the default event (ex: forgebox:manager.index) or the default route (ex:/forgebox) that ColdBox will use to create an entry link into the module. Similar to the default event setting. The SES interceptor will also use this to auto-register the module's routes if used by calling the method addModuleRoutes() for you.|