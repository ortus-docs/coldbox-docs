# Integration Testing

We will begin our adventure with integration testing. Integration testing allows you to test a real life request to your application without using a browser. Your test bundle will load a new virtual application for you to test each specification under it; all aspects of your application are loaded: caching, dependency injection, AOP, etc. 

Then you can target an event to test and verify its behavior accordingly. First of all, these type of tests go in your `integration` folder of your test harness or in the specific module folder if you are testing modules.

![](../../images/HandlerToTestRelationship.png)

Here are the basics to follow for integration testing:

* Create one test CFC for each event handler you would like to test
* Test CFC inherits from coldbox.system.testing.BaseTestCase
* The test CFC can have some annotations that tell the testing framework to what ColdBox application to connect to and test
* For each action in your event handler, create a `test{action}` method
* Execution of the event is done via the `execute()` method, which returns a request context object
* Most verifications and assertions are done via the contents of the request context object (request collections)


The creation of your test case based off the inherited BaseTestCase class will give you the ability to test your applications without the need of a browser to trigger the application startup routines, configuration and finally execution of your event. A key point here is that your test CFC has the machinery to talk to ANY ColdBox application in the same server only. By default it connects to the / or root of your server to look for the application. We have several annotations you can add to the test CFC component tag that alters this behavior:
