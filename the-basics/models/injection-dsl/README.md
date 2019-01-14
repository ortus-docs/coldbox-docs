# Injection DSL

Before we start building our objects, we need to understand how WireBox injects dependencies for us. You can define injections using the configuration inside the binder \(like any other DI framework\), but the easiest approach is to use our injection annotation and conventions \(called the injection DSL\). The injection DSL can be applied to `cfproperty`, `cfargument`, `cffunction` or called via `getInstance()` as we saw previously.

You can utilize the injection DSL by adding an attribute called `inject` to properties, arguments and functions. This attribute is like your code shouting, "**Hey, I need something here. Hello! I need something!**" That something might be another object, setting, cache, etc. The value of the `inject` attribute is a string that represents the concept of retrieving an object mapped in WireBox. It can be an object path, an object ID, a setting, a datasource, custom data, etc.

> **Info:** If you don't like annotations or find them obtrusive, you don't have to use them :\). Just leverage the WireBox binder to define dependencies instead.

Here are a few examples showing how to apply the injection DSL.

Property Annotation

```javascript
property name="service" inject="MyService";
```

Constructor Argument Annotation

```text
<cfargument name="setting" inject="MyService">
```

```javascript
/**
* @myDAO.inject model:MyDAO
*/
function init(myDAO){
}
```

Setter Method Annotation

```javascript
function setMyservice(MyService) inject="MyService"{
    variables.MyService = arguments.MyService;
}
```

You can learn more about the supported injection DSLs in [the WireBox Injection DSL documentation](http://wirebox.ortusbooks.com/content/injection_dsl/index.html).

