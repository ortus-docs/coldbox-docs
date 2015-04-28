# Handlers Isolation Testing

You can test handlers directly with no need of doing integration testing. This way you can unit test handlers directly very very easily. All you need to do is the following:

1. Create a test class that inherits from coldbox.system.testing.BaseHandlerTest
2. Create a component annotation called handler that equals the full path of the handler CFC to target for unit testing
3. Create an optional annotation called UDFLibraryFile that will be the path of the UDF library file that is declared in your ColdBox configuration or any other UDF file you load dynamically in your handlers.

This testing support class will create your handler, and decorate with mocking capabilities via [MockBox](http://wiki.coldbox.org/wiki/MockBox.cfm) and mock all the necessary companion objects around handlers. The following are the objects that are placed in the variables scope for you to use:

* handler : The target handler to test
* mockController : A mock ColdBox controller in use by the target handler
* mockRequestService : A mock request service object
* mockLogger : A mock logger class in use by the target handler
* mockLogBox : A mock LogBox class in use by the target handler
* mockFlash : A mock flash scope in use by the target handler

All of the mock objects are essentially the dependencies of handler objects. You have complete control over them as they are already mocked for you.

> **Important** We do not initialize your handlers for you. So if you have a init() method, you need to call it manually. Also note that this CFC is in isolation, you will have to mock all of its depenencies if needed. 