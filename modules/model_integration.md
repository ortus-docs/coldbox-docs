# Models

When you declare a module and you define a `models` folder then the framework automatically register all models in that folder for you using a namespace of `@moduleName`.

> **Info** Internally, ColdBox uses WireBox's `mapDirectory()` to map the entire `models` directory for you.


will register that folder as a scan location to the main parent's model integration [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) injector. This means, that whatever model object you place in the module's model folder will be available application wide. Not only that, but you can specifically create object bindings for the module by using the injected WireBox binder object. We suggest you always do object bindings as you can then request them with no issues at all. By default scan locations are done in order of search, so you could not be guaranteed to get the object you like.


```js
// WireBox Binder configuration
binder.map("forgeService@forgebox")
  .to("#moduleMapping#.model.ForgeService");
```

Here is a sample of autowiring my model objects from my module in my module's handlers:

```js
property name="forgeService" inject="id:forgeService@forgeBox";
property name="forgeService" inject="forgeService@forgeBox";
```

This tells WireBox to create a model object named *forgeService@forgeBox* and inject it for you.