# ColdBox Testing Classes

![](../images/ColdBoxTestingClasses.png)

Before we begin our adventures in testing, let's review what classes does ColdBox give you for testing and where you can find them. From the diagram you can see that our pivot class for testing is the TestBox `BaseSpec` class.

From that super class we have our own ColdBox `BaseTestCase` which is our base class for any testing in ColdBox and the class used for Integration Testing. We then spawn several child classes for targeted testing of different objects in your ColdBox applications:

BaseTestCase - Used for Integration Testing 
* BaseModelTest - Used for model object testing
* BasePluginTest - Used for plugin testing
* BaseInterceptorTest - Used for interceptor testing
* BaseHandlerTest - Used for isolated handler testing
