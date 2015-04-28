# Common Methods

The inheritance gives you access not only to MXUnit's assertions but also some great ColdBox methods. Please see the [API Docs](http://apidocs.coldbox.org/) for all the methods, or our nifty [Cheat Sheet](http://www.coldbox.org/downloads/ColdboxCheatSheet.pdf).

|Key|Description|
|--|--|
|$abort(), $dump(), $include(), $rethrow(), $throw() |CF Facades |
|announceInterception() |Announce an interception state with an intercept data |
|execute() |Execute an event to do integration test with|
|getAppMapping() |Getter for the application's mapping |
|getConfigMapping() |Getter for the config.xml location |
|getCacheBox() |Get a reference to [CacheBox](http://wiki.coldbox.org/wiki/CacheBox.cfm) |
|getColdBoxOCM() |Get a named cache provider|
|getController() |Getter for the ColdBox controller. |
|getMockController() |Getter for a mocked ColdBox controller. |
|getFlashScope() |Getter for the application's flash scope, great for asserting|
|getInterceptor() |Get a loaded interceptor|
|getLogBox() |Get the application's loaded [LogBox](http://wiki.coldbox.org/wiki/LogBox.cfm)|
|getModel() |Retrieve a model object|
|getRequestContext() |Getter for the current request collection object (event) |
|getWireBox() |Get a reference to [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm)|
|reset() |Resets everything in application scope and internally in a test case. Great when things go wrong|
|setup() |Where the virtual application request context get's cleared and created|
|beforeTests() |This MXUnit life-cycle method is where the ColdBox virtual controller and virtual application gets created and configured.|
|afterTests() |Where the virtual application is destroyed.|

