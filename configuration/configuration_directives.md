# Configure()
The basic configuration object has 1 method for application configuration called `configure()` where you will place all your configuration directives and settings:
```js
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
<br>
> **Note** : Please note that the only required directive is `coldbox.appName`.

<br>

## Directives

Inside of this configuration method you will place several core and third-party configuration structures that can alter your application settings and behavior. Below are the core directives you can define:

| Directive | Type | Description
| -- | -- |
| [cachebox](configuration_directives/cachebox.md) | struct | An optional structure used to configure CacheBox. If not setup the framework will use its default configuration found in `/coldbox/system/web/config/CacheBox.cfc`
| [coldbox](configuration_directives/coldbox.md) | struct | The main coldbox directives structure that holds all the coldbox settings.
| [conventions](configuration_directives/conventions.md) | struct | A structure where you will configure the application convention names
| [datasources](configuration_directives/datasources.md) | struct | An optional metadata structure for datasource definitions. THEY ARE NOT COLDFUSION REGISTRATIONS
| [environments](configuration_directives/environments.md) | struct | A structure where you will configure environment detection patterns
| [flash](configuration_directives/flash.md) | struct | A structure where you will configure the [FlashRAM](flash_ram/flash_ram.md)
| [interceptorSettings](configuration_directives/interceptorsettings.md) | struct | An optional structure to configure application wide interceptor behavior
| [interceptors](configuration_directives/interceptors.md) | array | An optional array of interceptor declarations for your application
| [layoutSettings](layoutsettings.md) | struct | A structure where you define how the layout manager behaves in your application
| [layouts](configuration_directives/layouts.md) | array | An array of layout declarations for implicit layout-view-folder pairings in your application
| [logbox](configuration_directives/logbox.md) | struct | An optional structure to configure the logging and messaging in your application via LogBox
| [modules](configuration_directives/modules.md) | struct | An optional structure to configure application wide module behavior
| [settings](configuration_directives/settings.md) | struct | A structure where you can put your own application settings
| [wirebox](configuration_directives/wirebox.md) | struct | An optional structure used to define how WireBox is loaded
