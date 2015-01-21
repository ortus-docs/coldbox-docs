# Settings

This element is used by the developer to set any values he/she would like to use in the application. This can be configuration settings, etc. Remember that you can use any of the already created variables in this CFC or the injected variables to concatenate or append your own settings. Also, since you are in a programmatic CFC you can pretty much do whatever you like here.

```js
// Custom Settings
settings = {
	useSkins = true,
	myCoolArray = [1,2,3,4],
	skinsPath = "views/skins",
	myUtil = createObject("component","#appmapping#.model.util.MyUtility")
};
```

You can then use the `getSetting()` method to retrieve them in your handlers/layouts/views and interceptors.  You can also use the injection DSL `coldbox:setting:{key}` to inject settings in your models or anywhere you like.