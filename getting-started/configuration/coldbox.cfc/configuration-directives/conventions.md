# Conventions

This element defines custom conventions for your application. By default, the framework has a default set of conventions that you need to adhere too. However, if you would like to implement your own conventions for a specific application, you can use this setting, otherwise do not declare it:

```javascript
//Conventions
conventions = {
    handlersLocation = "controllers",
    viewsLocation      = "views",
    layoutsLocation  = "views",
    modelsLocation      = "model",
    modulesLocation  = "modules",
    includesLocation = "includes",
    eventAction      = "index"
};
```

## Resulting Framework Settings

Behind the scenes, each key you override above (if any) is translated into one of the following framework settings, which is what the framework and its services actually read from at runtime. You can also read any of them directly via `getSetting( name )`:

| `conventions` key  | Framework Setting   | Default        |
| ------------------ | -------------------- | -------------- |
| `handlersLocation`  | `handlersConvention` | `handlers`     |
| `viewsLocation`     | `viewsConvention`     | `views`        |
| `layoutsLocation`   | `layoutsConvention`   | `layouts`      |
| `modelsLocation`    | `modelsConvention`    | `models`       |
| `modulesLocation`   | `modulesConvention`   | `modules`      |
| `includesLocation`  | `includesConvention`  | `includes`     |
| `eventAction`       | `eventAction`         | `index`        |

{% hint style="info" %}
`configConvention` (default `config.Coldbox`, the dot-notation invocation path used to locate your `config/ColdBox.cfc`) is also a framework setting, but it is resolved before conventions are parsed and cannot be overridden via the `conventions` struct.
{% endhint %}
