# ColdBox.cfc

The ColdBox configuration CFC is the heart of your ColdBox application. It contains the initialization variables for your application and extra information used by third-party modules and ultimately how your application boots up. In itself, it is also an event listener or [ColdBox Interceptor](/getting-started/configuration/coldbox.cfc/configuration-directives/interceptors.md), so it can listen to life-cycle events.

![]../images/Coldbox-cfc.jpg)

This CFC is instantiated by ColdBox and decorated at runtime so you can take advantage of some dependencies.

* **Controller** : Reference to the main ColdBox Controller object \(`coldbox.system.web.Controller`\)
* **AppMapping** : The location of the application on the web server
* **LogBoxConfig** : A reference to a LogBox configuration object

## Configuration Storage

Once the application starts up, a reference to the instantiated configuration CFC will be stored in the configuration settings with the key `coldboxConfig`. You can then retrieve it later in your handlers, modules, etc.

```javascript
// retrieve it
config = getSetting( 'coldboxConfig' );
// inject it
property name="config" inject="coldbox:setting:coldboxConfig";
```

