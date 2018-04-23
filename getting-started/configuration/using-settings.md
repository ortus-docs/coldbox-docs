# Using Settings

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/ControllerWithSettingStructures.jpg)

The ColdBox Controller \(stored in ColdFusion `application` scope\) stores all your **application** settings and also your **system** settings:

* **ColdboxSettings** : Framework specific system settings
* **ConfigSettings** : Your application settings

You can use the following methods to retrieve/set/validate settings in your handlers/layouts/views and interceptors:

{% code-tabs %}
{% code-tabs-item title="FrameworkSuperType.cfc" %}
```javascript
/**
 * Get a setting from the system
 * @name The key of the setting
 * @fwSetting Retrieve from the config or fw settings, defaults to config
 * @defaultValue If not found in config, default return value
 */
function getSetting( required name, boolean fwSetting=false, defaultValue )

/**
 * Verify a setting from the system
 * @name The key of the setting
 * @fwSetting Retrieve from the config or fw settings, defaults to config
 */
boolean function settingExists( required name, boolean fwSetting=false )

/**
 * Set a new setting in the system
 * @name The key of the setting
 * @value The value of the setting
 *
 * @return FrameworkSuperType
 */
any function setSetting( required name, required value )
```
{% endcode-tabs-item %}
{% endcode-tabs %}

You can also get access to these methods via the ColdBox Controller component:

```javascript
controller.getSetting()
controller.setSetting()
controller.settingExists()
controller.getConfigSettings()
controller.getColdBoxSettings()
```

## Injecting Settings

You can use the WireBox injection DSL to inject settings in your models or non-coldbox objects. Below are the available DSL notations:

* `coldbox:setting:{key}` : Inject a specified config setting key
* `coldbox:fwsetting:{key}` : Inject a specified system setting key
* `coldbox:configSettings` : Inject a reference to the application settings structure
* `coldbox:fwSettings` : Inject a reference to the ColdBox System settings structure

```javascript
component{

    property name="mysetting" inject="coldbox:setting:mysetting";
    property name="path" inject="coldbox:fwSetting:path";
    property name="config" inject="coldbox:configSettings";
    property name="settings" inject="coldbox:fwSettings";

}
```

