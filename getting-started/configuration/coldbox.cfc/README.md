# ColdBox.cfc

The ColdBox configuration CFC is the heart of your ColdBox application. It contains the initialization variables for your application and extra information used by third-party modules and ultimately how your application boots up. In itself, it is also an event listener or [ColdBox Interceptor](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/full/configuration/coldboxcfc/interceptors/interceptors.md), so it can listen to life-cycle events.

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/Coldbox-cfc.jpg)

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

