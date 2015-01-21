# LogBox

The logBox structure is based on the LogBox declaration DSL, see the  [LogBox Documentation](http://wiki.coldbox.org/wiki/LogBox.cfm) for much more information.

```js
//LogBox DSL
logBox = {
    // The configuration file to use for operation, instead of using this structure
    configFile = "config/LogBox.cfc",
	// Define Appenders
	appenders = {
		coldboxTracer = { 
		  class="coldbox.system.logging.appenders.ColdboxTracerAppender",
		  layout="coldbox.testing.cases.logging.MockLayout", 
		  properties = {
		    name = "awesome"
		  },
		  rollingFile = {
		    class="coldbox.system.logging.appenders.AsyncRollingFileAppender",
			levelMax="WARN",
			levelMin="FATAL",
			properties={
			  filePath="/#appMapping#/logs",
			  autoExpand="true",
			  fileMaxSize="3000",
			  fileMaxArchives="5"
			}
		  }
		}
	}
};
```

**ConfigFile**

You can use a configuration CFC instead of inline configuration by using this setting.  The default value is of `config/LogBox.cfc`, so by convention you can just use that location.  If no values are defined or no config file exists the default configuration file is `coldbox/system/web/config/LogBox`
