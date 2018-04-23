# ModuleSettings

This structure is used to house module configurations. Please refer to each module's documentation on how to create the configuration structures.  Usually the keys will match the name of the module to  be configured.

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

