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

