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

## Retrieving Settings

You can use the following methods to retrieve/set/validate settings in your handlers/layouts/views and interceptors:

```js
/**
* Get a setting from the system
* @name.hint The key of the setting
* @fwSetting.hint Retrieve from the config or fw settings, defaults to config
* @defaultValue.hint If not found in config, default return value
*/
function getSetting( required name, boolean fwSetting=false, defaultValue )

/**
* Verify a setting from the system
* @name.hint The key of the setting
* @fwSetting.hint Retrieve from the config or fw settings, defaults to config
*/
boolean function settingExists( required name, boolean fwSetting=false )

/**
* Set a new setting in the system
* @name.hint The key of the setting
* @value.hint The value of the setting
*
* @return FrameworkSuperType
*/
any function setSetting( required name, required value )
```

You can also use the injection DSL `coldbox:setting:{key}` to inject settings in your models or anywhere you like.