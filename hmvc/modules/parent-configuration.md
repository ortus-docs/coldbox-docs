# Parent Configuration

There are a few parent application settings when dealing with modules. In your `ColdBox.cfc` you can have a `modules` structure with some configuration settings.

```javascript
modules = {
    // An array or list of the module names that will load ONLY
    include = [],
    // An array or list of the module names that will be EXCLUDED
    exclude = ["paidModule1","paidModule2"]
};
```

* `include` : An array of module names that will ONLY be loaded when the app starts
* `exclude` : An array of module names that will NOT be loaded when the app starts



