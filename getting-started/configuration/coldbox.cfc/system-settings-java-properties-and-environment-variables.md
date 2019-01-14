# System Settings \(Java Properties and Environment Variables\)

ColdBox makes it easy to access the configuration stored in your Java system properties and your server's environment variables, even if you don't know which one it is in! Three methods are provided for your convenience:

| Name | Arguments | Description |
| :--- | :--- | :--- |
| `getSystemSetting` | `( key, defaultValue )` | Looks for `key` in properties first, env second. Returns the `defaultValue` if neither exist. |
| `getSystemProperty` | `( key, defaultValue )` | Returns the Java System property for `key`. Returns the `defaultValue` if it does not exist. |
| `getEnv` | `( key, defaultValue )` | Returns the server environment variable for `key`. Returns the `defaultValue` if it does not exist. |

## Accessing System Settings in `config/ColdBox.cfc` or a `ModuleConfig.cfc`

If you are inside `config/ColdBox.cfc` or a `ModuleConfig.cfc` or a `config/WireBox.cfc` you can use the three system settings functions directly! No additional work required.

## Accessing System Settings in `Application.cfc`

If you would like to access these methods in your `Application.cfc`, create an instance of `coldbox.system.core.util.Util` and access them off of that component. This is required when adding a datasource from environment variables.

Example:

{% code-tabs %}
{% code-tabs-item title="Application.cfc" %}
```javascript
component {

    variables.util = new coldbox.system.core.util.Util();

    this.datasources[ "my_datasource" ] = {
        driver = util.getSystemSetting( "DB_DRIVER" ),
        host = util.getSystemSetting( "DB_HOST" ),
        port = util.getSystemSetting( "DB_PORT" ),
        database = util.getSystemSetting( "DB_DATABASE" ),
        username = util.getSystemSetting( "DB_USERNAME" ),
        password = util.getSystemSetting( "DB_PASSWORD" )
    };

}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Accessing System Settings in other files

If you need to access these configuration values in other components, consider adding the values to your [ColdBox settings](configuration-directives/settings.md) and injecting the values into your other components [via dependecy injection.](../using-settings.md)

