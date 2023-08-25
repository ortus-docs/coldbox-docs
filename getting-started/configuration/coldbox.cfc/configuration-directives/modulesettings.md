# ModuleSettings

## Option 1: Coldbox Config
This structure within config/Coldbox.cfc is used to house module configurations. Please refer to each module's documentation on how to create the configuration structures.  Usually the keys will match the name of the module to  be configured.

```javascript
component {

     function configure() {

         moduleSettings = {
             myModule = {
                someSetting = "overridden"
             }
        };
    }
}
```

## Option 2: Config Object Override
Starting in ColdBox 7, you can store module configurations as their own configuration file within the application’s config folder outside of the config/Coldbox.cfc.
The naming convention is config/modules/{moduleName}.cfc

The configuration CFC will have one configure() method that is expected to return a struct of configuration settings as you did before in the moduleSettings
```
component{

    function configure(){
        return {
            key : value
        };
    }

}
```

### Injections
Just like a ModuleConfig this configuration override also gets many injections:
* controller
* coldboxVersion
* appMapping
* moduleMapping
* modulePath
* logBox
* log
* wirebox
* binder
* cachebox
* getJavaSystem
* getSystemSetting
* getSystemProperty
* getEnv
* appRouter
* router
 
### Env Support
This module configuration object will also inherit the ModuleConfig.cfc behavior that if you create methods with the same name as the environment you are on, it will execute it for you as well.
```
function development( original ){
   // add overides to the original struct
}
```
Then you can change the original struct as you see fit for that environment.
