# Configuration Directives

The basic configuration object has 1 method for application configuration called `configure()` where you will place all your configuration directives and settings:

```javascript
/**
* A simple CFC that configures a ColdBox application.  You can even extend, compose, strategize and do your OO goodness.
*/
component{
    // Mandatory configuration method
    function configure(){
        coldbox = {
          appName ="My App"
        };
    }
}
```

> **Note** : Please note that the only required directive is `coldbox.appName`.

## Directives

Inside of this configuration method you will place several core and third-party configuration structures that can alter your application settings and behavior. Below are the core directives you can define:

| Directive | Type | Description |
| --- | --- |
| [cachebox](cachebox.md) | struct | An optional structure used to configure CacheBox. If not setup the framework will use its default configuration found in `/coldbox/system/web/config/CacheBox.cfc` |
| [coldbox](coldbox.md) | struct | The main coldbox directives structure that holds all the coldbox settings. |
| [conventions](conventions.md) | struct | A structure where you will configure the application convention names |
| [datasources]() | struct | An optional metadata structure for datasource definitions. THEY ARE NOT COLDFUSION REGISTRATIONS |
| [environments](environments.md) | struct | A structure where you will configure environment detection patterns |
| [flash](flash.md) | struct | A structure where you will configure the [FlashRAM](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/full/configuration/coldboxcfc/configuration_directives/flash_ram/flash_ram.md) |
| [interceptorSettings](interceptorsettings.md) | struct | An optional structure to configure application wide interceptor behavior |
| [interceptors](interceptors.md) | array | An optional array of interceptor declarations for your application |
| [layoutSettings](layoutsettings.md) | struct | A structure where you define how the layout manager behaves in your application |
| [layouts](layouts.md) | array | An array of layout declarations for implicit layout-view-folder pairings in your application |
| [logbox](logbox.md) | struct | An optional structure to configure the logging and messaging in your application via LogBox |
| [modules](modules.md) | struct | An optional structure to configure application wide module behavior |
| [moduleSettings](modulesettings.md) | struct | An optional structure to configure individual modules installed in your application. |
| [settings](settings.md) | struct | A structure where you can put your own application settings |
| [wirebox](wirebox.md) | struct | An optional structure used to define how WireBox is loaded |

