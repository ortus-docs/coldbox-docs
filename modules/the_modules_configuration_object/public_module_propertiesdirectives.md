# Public Module Properties/Directives

First of all, we must define some public properties that can easily identify or provide directives for the module:


|Property|Type|Required|Default|Description|
|--|--|--|--|--|
| title|string|false|---|The title of the module|
| author|string|false|---|The author of the module|
| webURL|string|false|---|The web URL associated with this module. Maybe for documentation, blog, links, etc.|
| description|string|false|---|A short description about the module |
| version|string|false|---|The version of the module|
| viewParentLookup |boolean|false|true|If true, coldbox checks for views in the parent overrides first, then in the module. If false, coldbox checks for views in the module first, then the parent.|
| layoutParentLookup |boolean|false|true|If true, coldbox checks for layouts in the parent overrides first, then in the module. If false, coldbox checks for layouts in the module first, then the parent. |
| entryPoint|route|false|---| The module's  default route (ex:`/forgebox`) that ColdBox will use to create an entry point pattern link into the module. Similar to the default event setting. The SES interceptor will also use this to auto-register the module's routes if used by calling the method `addModuleRoutes()` for you.|
| activate | boolean | false | true | You can tell ColdBox to register the module but NOT to activate it. By default, all modules activate.
| parseParentSettings | boolean | false | true | If true, ColdBox will merge any settings found in `moduleSettings[ this.modelNamespace ]` in the `config/ColdBox.cfc` file with the module settings, overriding them where the keys are the same.  Otherwise, settings in the module will override the parent configuration.
| aliases | array | false | [] | An array of names that can be used to execute the module instead of only the module folder name
| autoMapModels | boolean | false | true | Will automatically map all model objects under the models folder in WireBox using `@modulename` as part of the alias.
| cfmapping | string | false | empty | The ColdFusion mapping that should be registered for you that points to the root of the module.
| disabled | boolean | false | false | You can manually disable a module from loading and registering
| dependencies | array | false | [] | An array of dependent module names. All dependencies will be registered and activated FIRST before the module declaring them.
| modelNamespace | string | false | *moduleName* | The name of the namespace to use when registering models in WireBox. By default it uses the name of the module.

Below you can see an example of declarations for the configuration object:

```js
component{
    // Module Properties
    this.title      = "My Test Module";
    this.author       = "Luis Majano";
    this.webURL       = "http://www.coldbox.org";
    this.description    = "A funky test module";
    this.version      = "1.0.0";
    // If true, looks for views in the parent first, if not found, then in the module. Else vice-versa
    this.viewParentLookup   = true;
    // If true, looks for layouts in the parent first, if not found, then in module. Else vice-versa
    this.layoutParentLookup = true;
    // The module entry point using SES
    this.entryPoint     = "/testing";
    this.autoMapModels = true;
    this.modelNamespace = "store";
    this.aliases = [ "store", "ecommerce", "shop" ];
    this.cfmapping = "cbstore";
    this.parseParentSettings = true;
    this.dependencies = [ "JavaLoader", "CFCouchbase" ];
  
  function configure(){

  }
}
```

