# Using Settings

<img src="../images/ControllerWithSettingStructures.jpg" >

ColdBox uses two internal structures in order to configure and run your applications. One is the **ColdBox** settings and the other is the **Configuration** settings, which are created on the application's initial request. The ColdBox Settings and the Configuration Settings reside inside the ColdBox application controller, which is the object that models your application and lives in the `application` scope. 

> **Hint** : Please remember that your application can be reinitialized by using the `fwreinit=1` URL param.

* **ColdboxSettings** : Framework specific settings.
* **ConfigSettings** : Your application settings setup in the `settings` configuration directive.


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

# Injecting Settings

You can use the injection DSL `coldbox:setting:{key}` to inject settings in your models or anywhere you like.

