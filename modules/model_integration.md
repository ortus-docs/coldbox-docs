# Models

When you declare a module and you define a `models` folder then the framework automatically register all models in that folder for you using a namespace of `@moduleName`.  This means that all models are registered according to their CFC name plus the namespace.

> **Info** Internally, ColdBox uses WireBox's `mapDirectory()` to map the entire `models` directory for you.

 Let's say you have a module called `store` and a `OrderService.cfc` inside of the `models` folder.  That object will have a WireBox id of `OrderService@store`.
 
```js
property name="orderService" inject="OrderService@store";
```

> **Hint** You can alter this behavior by setting the `this.autoMapModels` configuration setting to **false**. You can also alter the namespace used via the `this.modelNamespace` configuration property.