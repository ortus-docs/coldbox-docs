# Retrieving Module Settings

Modules configurations are stored in the host parent configuration structure under a `modules` key according to their name. To retrieve them you can do

* `getModuleSettings( module )` : Returns the structure of module settings by the module name.
* `getModuleConfig( module )` : Returns the module's configuration structure

## ColdBox Injection DSL

You can also use our injection DSL to inject module settings and configurations:

```js
// Inject all module settings
property name="settings" inject="coldbox:modulesettings:{module}";
// Inject the module configuration structure
property name="config" inject="coldbox:moduleConfig:{module}";
// Inject a single module setting
property name="customSetting" inject="coldbox:setting:customSetting@{module}";
```

* `coldbox:setting:mySetting@module` : You can now inject easily a module setting by using the @module directive.
* `coldbox:moduleSettings:{module}` : You can now inject the entire module settings structure
* `coldbox:moduleConfig:{module}` : You can now inject the entire module configuration structure 