# Retrieving & Interacting With Module Settings

We now understand that the modules configurations are stored in the host parent configuration structure under the modules key. You can do a very hefty dot notation to retrieve module settings but we have created a shorthand method that is available in the [framework supertype](http://wiki.coldbox.org/wiki/ReservedWordsAndMethods.cfm) and thus available in all handlers, plugins, interceptors, layouts and views:

* getModuleSettings( module ) : Returns the structure of module settings by the module name.

You can also use our injection DSL to inject module settings and configurations:

```js
// Inject all module settings
property name="settings" inject="coldbox:modulesettings:{module}";
// Inject the module configuration structure
property name="config" inject="coldbox:moduleConfig:{module}";
// Inject a single module setting
property name="customSetting" inject="coldbox:setting:customSetting@{module}";
```

