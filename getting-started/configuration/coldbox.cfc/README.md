---
description: The ColdBox.cfc is the main applications' configuration object.
---

# ColdBox.cfc

The ColdBox configuration CFC is the heart of your ColdBox application. It contains the initialization variables for your application and extra information used by third-party modules and ultimately how your application boots up. In itself, it is also an event listener or [ColdBox Interceptor](configuration-directives/interceptors.md), so it can listen to life-cycle events of your application.



![ColdBox.cfc EcoSystem](../../../.gitbook/assets/Coldbox-cfc.jpg)

This CFC is instantiated by ColdBox and decorated at runtime so you can take advantage of some dependencies.  Here is a table of the automatic injection this object has:

| **Property**          | **Description**                                                                                               |
| --------------------- | ------------------------------------------------------------------------------------------------------------- |
| `appMapping`          | The ColdBox app mapping                                                                                       |
| `coldboxVersion`      | The version of the framework                                                                                  |
| `controller`          | The ColdBox running app controller                                                                            |
| `logBoxConfig`        | A reference to a LogBox configuration object                                                                  |
| `getJavaSystem()`     | Function to get access to the java system                                                                     |
| `getSystemSetting()`  | Retrieve a Java System property or env value by name. It looks at properties first then environment variables |
| `getSystemProperty()` | Retrieve a Java System property value by key                                                                  |
| `getEnv()`            | Retrieve a Java System environment value by name                                                              |
| `webMapping`          | The application's web mapping                                                                                 |



## Configuration Storage

Once the application starts up, a reference to the instantiated configuration CFC will be stored in the configuration settings inside the ColdBox Main Controller (`application.cbController`) with the key `coldboxConfig`. You can then retrieve it later in your handlers, interceptors, modules, etc if you need to.

```javascript
// retrieve it
config = getSetting( 'coldboxConfig' );

// inject it
property name="config" inject="coldbox:setting:coldboxConfig";
```

## Configuration Interceptor

![ColdBox Event Listeners](../../../.gitbook/assets/eventdriven.jpg)

Another cool concept for the Configuration CFC is that it is also registered as a [ColdBox Interceptor](../../../the-basics/interceptors/) once the application starts up automatically for you.  You can create functions that will listen to application events by simply registering them by name:

```javascript
function preProcess( event, interceptData, buffer, rc, prc ){
    writeDump( 'I just hijacked your app!' );abort;
}
```

{% hint style="danger" %}
Note that the config CFC does not have the same variables mixed into it that a "normal" interceptor has. You can still access everything you need, but will need to get it from the `controller` in the variables scope.
{% endhint %}

```javascript
function preRender( event, interceptData, buffer, rc, prc ){
    controller.getWirebox().getInstance( 'loggerService' ).doSomething();
}
```
