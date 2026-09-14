# System Settings (Java Properties and Environment Variables)

ColdBox makes it easy to access the configuration stored in your Java system properties and your server's environment variables, even if you don't know which one it is in! Three methods are provided for your convenience:

| Name                | Arguments               | Description                                                                                         |
| ------------------- | ----------------------- | --------------------------------------------------------------------------------------------------- |
| `getSystemSetting`  | `( key, defaultValue )` | Looks for `key` in properties first, env second. Returns the `defaultValue` if neither exist.       |
| `getSystemProperty` | `( key, defaultValue )` | Returns the Java System property for `key`. Returns the `defaultValue` if it does not exist.        |
| `getEnv`            | `( key, defaultValue )` | Returns the server environment variable for `key`. Returns the `defaultValue` if it does not exist. |

## Accessing System Settings in `config/ColdBox.bx` (or `.cfc` for CFML) or a `ModuleConfig.bx` (or `.cfc` for CFML)

If you are inside `config/ColdBox.bx` (or `.cfc` for CFML) or a `ModuleConfig.bx` (or `.cfc` for CFML) or a `config/WireBox.bx` (or `.cfc` for CFML) you can use the three system settings functions directly! No additional work required.

## Accessing System Settings in `Application.bx` (or `.cfc` for CFML)

If you would like to access these methods in your `Application.bx` (or `.cfc` for CFML), create an instance of `coldbox.system.core.delegates.Env` and access them off of that class. This is required when adding a datasource from environment variables.

Example:

{% tabs %}
{% tab title="BoxLang" %}
{% code title="Application.cfc" %}
```js

class {

    variables.env = new coldbox.system.core.delegates.Env();

    this.datasources[ "my_datasource" ] = {
        driver = env.getSystemSetting( "DB_DRIVER" ),
        host = env.getSystemSetting( "DB_HOST" ),
        port = env.getSystemSetting( "DB_PORT" ),
        database = env.getSystemSetting( "DB_DATABASE" ),
        username = env.getSystemSetting( "DB_USERNAME" ),
        password = env.getSystemSetting( "DB_PASSWORD" )
    };

}
```
{% endcode %}
{% endtab %}
{% tab title="CFML" %}
{% code title="Application.cfc" %}
```cfscript

component {

    variables.env = new coldbox.system.core.delegates.Env();

    this.datasources[ "my_datasource" ] = {
        driver = env.getSystemSetting( "DB_DRIVER" ),
        host = env.getSystemSetting( "DB_HOST" ),
        port = env.getSystemSetting( "DB_PORT" ),
        database = env.getSystemSetting( "DB_DATABASE" ),
        username = env.getSystemSetting( "DB_USERNAME" ),
        password = env.getSystemSetting( "DB_PASSWORD" )
    };

}
```
{% endcode %}
{% endtab %}
{% endtabs %}

## Accessing System Settings in other files

If you need to access these configuration values in other classes, consider adding the values to your [ColdBox settings](configuration-directives/settings.md) and injecting the values into your other classes [via dependency injection.](../using-settings.md)
