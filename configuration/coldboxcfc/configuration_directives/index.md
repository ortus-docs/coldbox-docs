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
| [cachebox](cachebox.html) | struct | An optional structure used to configure CacheBox. If not setup the framework will use its default configuration found in `/coldbox/system/web/config/CacheBox.cfc`
| [coldbox](coldbox.html) | struct | The main coldbox directives structure that holds all the coldbox settings.
| [conventions](conventions.html) | struct | A structure where you will configure the application convention names
| [datasources](datasources.html) | struct | An optional metadata structure for datasource definitions. THEY ARE NOT COLDFUSION REGISTRATIONS
| [environments](environments.html) | struct | A structure where you will configure environment detection patterns
| [flash](flash.html) | struct | A structure where you will configure the [FlashRAM](flash_ram/flash_ram.md)
| [interceptorSettings](interceptorsettings.html) | struct | An optional structure to configure application wide interceptor behavior
| [interceptors](interceptors.html) | array | An optional array of interceptor declarations for your application
| [layoutSettings](layoutsettings.html) | struct | A structure where you define how the layout manager behaves in your application
| [layouts](layouts.html) | array | An array of layout declarations for implicit layout-view-folder pairings in your application
| [logbox](logbox.html) | struct | An optional structure to configure the logging and messaging in your application via LogBox
| [modules](modules.html) | struct | An optional structure to configure application wide module behavior
| [settings](settings.html) | struct | A structure where you can put your own application settings
| [wirebox](wirebox.html) | struct | An optional structure used to define how WireBox is loaded
