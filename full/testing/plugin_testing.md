# Plugin Testing

You can test ColdBox plugins directly with no need of doing integration testing. This way you can unit test plugins directly very very easily with mocking integration built-in. All you need to do is the following:

1. Create a test class that inherits from coldbox.system.testing.BasePluginTest
2. Create a component annotation called plugin that equals the full path of the plugin to target for testing

This testing support class will create your plugin, and decorate with mocking capabilities via [MockBox](http://wiki.coldbox.org/wiki/MockBox.cfm) and mock all the necessary companion objects around plugins. The following are the objects that are placed in the variables scope for you to use:


* plugin : The target plugin to test
* mockController : A mock ColdBox controller in use by the target plugin
* mockRequestService : A mock request service object
* mockLogger : A mock logger class in use by the target plugin
* mockLogBox : A mock LogBox class in use by the target plugin
* mockFlash : A mock flash scope in use by the target plugin

All of the mock objects are essentially the dependencies of plugin objects. You have complete control over them as they are already mocked for you. We actually use this approach to test all shipped ColdBox plugins.

> **Important**  We do not initialize your plugins for you. This is your job as you might need some mocking first. 

Basic Setup

```js
component extends="coldbox.system.testing.BasePluginTest" plugin="myapp.plugins.CoolPlugin"{

  // Just create test methods, no need to use the setup() method unless you want to:
  function setup(){
      super.setup();
      // test custom constructor
      plugin.init();
  }

}
```

Real Sample 

```js
<cfcomponent extends="coldbox.system.testing.BasePluginTest" plugin="coldbox.system.plugins.HTMLHelper">
<cfscript>
function testaddAssetJS(){
	var mockEvent = getMockRequestContext();
	mockRequestService.$("getContext", mockEvent);
	
	// mock the plugin's htmlhead method
	plugin.$("$htmlhead");
	
	// Call method to test
	plugin.addAsset('test.js,luis.js');
	
	debug( plugin.$callLog().$htmlhead);
	
	// test duplicate call
	assertEquals('<script src="test.js" type="text/javascript"></script><script src="luis.js" type="text/javascript"></script>' , plugin.$callLog().$htmlhead[1][1] );
	plugin.addAsset('test.js');
	assertEquals(1, arrayLen(plugin.$callLog().$htmlHead) );
}

function testTableORM(){
	data = entityLoad("User");
	
	str = plugin.table(data=data,includes="firstName");
	debug(str);
	assertEquals('<table><thead><tr><th>firstName</th></tr></thead><tbody><tr><td>Joe</td></tr><tr><td>Luis</td></tr></tbody></table>',str);
}
</cfscript>
</cfcomponent>
```
