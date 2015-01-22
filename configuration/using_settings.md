# Using Settings

<img src="../images/ControllerWithSettingStructures.jpg" >

ColdBox uses two internal structures in order to configure and run your applications. One is the **ColdBox** settings and the other is the **Configuration** settings, which are created on the application's initial request. The ColdBox Settings and the Configuration Settings reside inside the ColdBox application controller, which is the object that models your application and lives in the `application` scope. 

> **Hint** : Please remember that your application can be reinitialized by using the `fwreinit=1` URL param.

* **ColdboxSettings** : Framework specific settings.
* **ConfigSettings** : Your application settings setup in the `settings` [configuration directive](configuration_directives/settings.md).

You can use the following methods to retrieve/set/validate settings in your handlers/layouts/views and interceptors:

```js
/**
* Get a setting from the system
* @name.hint The key of the setting
* @fwSetting.hint Retrieve from the config or fw settings, defaults to config
* @defaultValue.hint If not found in config, default return value
*/
function getSetting( required name, boolean fwSetting=false, defaultValue )

/**
* Verify a setting from the system
* @name.hint The key of the setting
* @fwSetting.hint Retrieve from the config or fw settings, defaults to config
*/
boolean function settingExists( required name, boolean fwSetting=false )

/**
* Set a new setting in the system
* @name.hint The key of the setting
* @value.hint The value of the setting
*
* @return FrameworkSuperType
*/
any function setSetting( required name, required value )
```

You can also get access to these methods via the ColdBox Main Controller:

```js
controller.getSetting()
controller.setSetting()
controller.settingExists()
controller.getConfigSettings()
controller.getColdBoxSettings()
```

# Injecting Settings

You can use the WireBox injection DSL to inject settings in your models or anywhere you like. Below are the available DSL notations:

* `coldbox:setting:{key}` : Inject a specified config setting key
* `coldbox:fwsetting:{key}` : Inject a specified ColdBox setting key
* `coldbox:configSettings` : Inject a reference to the Config Settings
* `coldbox:fwSettings` : Inject a reference to the ColdBox Settings

```js
component{

    property name="mysetting" inject="coldbox:setting:mysetting";
    property name="path" inject="coldbox:fwSetting:path";
    property name="config" inject="coldbox:configSettings";
    property name="settings" inject="coldbox:fwSettings";

}
```

> **Info** : This approach is the one you will use to inject settings in your models.



