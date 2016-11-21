# Retrieving Module Settings

Modules configurations are stored in the host parent configuration structure under a `modules` key according to their name. To retrieve them you can do

* `getModuleSettings( module )` : Returns the structure of module settings by the module name.
* `getModuleConfig( module )` : Returns the module's configuration structure

# Injecting Module Settings

You can also use our injection DSL to inject module settings and configurations:

```js
// Inject all module settings
property name="settings" inject="coldbox:modulesettings:{module}";
// Inject the module configuration structure
property name="config" inject="coldbox:moduleConfig:{module}";
// Inject a single module setting
property name="customSetting" inject="coldbox:setting:customSetting@{module}";
```

The injection DSL pattern is the following:

* `coldbox:setting:mySetting@module` : You can now inject easily a module setting by using the @module directive.
* `coldbox:moduleSettings:{module}` : You can now inject the entire module settings structure
* `coldbox:moduleConfig:{module}` : You can now inject the entire module configuration structure 

# Overriding Module Settings

If you have `parseParentSettings` set to true in your `ModuleConfig.cfc` (which is the default), ColdBox will look for a struct inside the `moduleSettings` struct of your `config/ColdBox.cfc` with a key equal to your module's `this.modelNamespace` (default is the module name) and merge them with your modules `settings` struct (as defined in your module's `configure()` method) with the parent settings overwritting the module settings.  This allows a user of your module to easily overwrite specific settings for their needs.

```js
// myModule/ModuleConfig.cfc
component {
  function configure() {
    settings = {
      someSetting = "default",
      anotherSetting = "default"
    };
  }
}

// config/ColdBox.cfc
component {
  function configure() {
    moduleSettings = {
      myModule = {
        someSetting = "overridden" 
      }
    };
  }
}

// end result
{
  someSetting = "overridden",
  anotherSetting = "default"
}
```

If `parseParentSettings` is set to `false`, your module's `settings` will instead overwrite the settings set in the same `moduleSettings` struct.

# Using Overridden Settings in your `ModuleConfig.cfc`

If you want to use the overridden settings in your `ModuleConfig.cfc`, you will need to use it in the `postModuleLoad` interceptor.  Remember, all your modules register the `ModuleConfig.cfc` as an interceptor, so all you need to do is add the `postModuleLoad` function and you're off!

```js
function postModuleLoad( event, interceptData, buffer, rc, prc ) {
  binder.map( "MyAlias@MyModule" )
    .to( "#moduleMapping#.models.#settings.moduleName#" );
}
```