# Environment Control

If you are using per-environment control in your parent application via the `ColdBox.cfc`, you can also use that in your Module Configuration object. So the same conventions that are used in the parent configuration can be used in the module; having the name of the environment match a method name in your module config. So if the following environments are declared in your parent configuration file and the dev environment is detected, the dev() method is called in your parent:

```js
environments = {
  dev = "^railo.*,^cf.*,^local.*"
};

function dev(){
  // my overrides here
  coldbox.handlerCaching = false;
}
```

But if you declare that `dev()` method in your Module Configuration object, it will be called as well after your `configure()` method is called:

```js
function configure(){}

function dev(){
  // override module settings for dev here.
}
```

