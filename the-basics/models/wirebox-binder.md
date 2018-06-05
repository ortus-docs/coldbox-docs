# WireBox Binder

You can have an optional WireBox configuration binder that can fine-tune the WireBox engine and also where you can create object mappings, and even more model locations by convention. Usually you will find this binder by convention in your `config/WireBox.cfc` location and it looks like this:

```javascript
component extends="coldbox.system.ioc.config.Binder"{

    function configure(){

        // Configure WireBox
        wireBox = {
            // Scope registration, automatically register a wirebox injector instance on any CF scope
            // By default it registeres itself on application scope
            scopeRegistration = {
                enabled = true,
                scope   = "application", // server, cluster, session, application
                key        = "wireBox"
            },

            // Custom DSL Namespace registrations
            customDSL = {
                // namespace = "mapping name"
            },

            // Custom Storage Scopes
            customScopes = {
                // annotationName = "mapping name"
            },

            // Package scan locations or model external locations by convention
            scanLocations = [],

            // Stop Recursions
            stopRecursions = [],

            // Parent Injector to assign to the configured injector, this must be an object reference
            parentInjector = "",

            // Register all event listeners here, they are created in the specified order
            listeners = [
                // { class="", name="", properties={} }
            ]            
        };

        // Map Bindings below
    }    
}
```

Please refer to the full Binder documentation: \([http://wirebox.ortusbooks.com/configuration/configuring-wirebox/](http://wirebox.ortusbooks.com/configuration/configuring-wirebox/)\) for further inspection.

## Mappings

By default, all objects that you place in the `models` folder are available to your application by their name. So if you create a new model object: `models/MyService.cfc`, you can refer to it as `MyService` in your application. However, if you create a model object: `models/security/SecurityService.cfc` it will be available as `security.SecurityService`. This is great and dandy, but when refactoring comes to play you will have to refactor all references to the dot-notation paths. This is where mappings come into play.

WireBox has several methods for mappings, the easiest of them are the following:

* `map( alias ).to( cfc.path )`
* `mapPath( path )`
* `mapDirectory( packagePath, include, exclude, influence, filter, namespace )`

```javascript
// map the service with an alias.
map( "SecurityService" ).to( "models.security.SecurityService" );

// map the entire models directory by CFC name as the alias
mapDirectory( "models" );
```

