# LogBox

The logBox structure is based on the LogBox declaration DSL, see the  [LogBox Documentation](http://wiki.coldbox.org/wiki/LogBox.cfm) for much more information.

```js
//LogBox DSL
logBox = {
    // The configuration file to use for operation, instead of using this structure
    configFile = "config/LogBox.cfc",
	// Appenders
	appenders = {
		appenderName = {
			class="class.to.appender", 
			layout="class.to.layout",
			levelMin=0,
			levelMax=4,
			properties={
				name  = value,
				prop2 = value 2
			}
	},
	// Root Logger
	root = {levelMin="FATAL", levelMax="DEBUG", appenders="*"},
	// Granualr Categories
	categories = {
		"coldbox.system" = { levelMin="FATAL", levelMax="INFO", appenders="*"},
		"model.security" = { levelMax="DEBUG", appenders="console"}
	}
	// Implicit categories
	debug  = ["coldbox.system.interceptors"],
	info   = ["model.class", "model2.class2"],
	warn   = ["model.class", "model2.class2"],
	error  = ["model.class", "model2.class2"],
	fatal  = ["model.class", "model2.class2"],
	off    = ["model.class", "model2.class2"]
};
```

> **Info** : If you do not define a logBox DSL structure, the framework will look for the default configuration file `config/LogBox.cfc`. If it does not find it, then it will use the framework's default logging settings.


**ConfigFile**

You can use a configuration CFC instead of inline configuration by using this setting.  The default value is of `config/LogBox.cfc`, so by convention you can just use that location.  If no values are defined or no config file exists the default configuration file is `coldbox/system/web/config/LogBox.cfc`.
