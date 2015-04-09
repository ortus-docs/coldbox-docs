# Model Integration

We have a complete guide dedicated to Model Integration but we wanted to review a little here since event handlers need to talk to the model layer all the time. By default you can interact with your models from your event handlers in two ways:

*  Dependency Injection
*  Request model objects


ColdBox offers its own dependency injection framework, [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm), which allows you by convention to talk to your model objects. However, ColdBox also allows you to connect to [ColdSpring](http://wiki.coldbox.org/wiki/Plugins:ColdspringIntegration.cfm), [LightWire](http://wiki.coldbox.org/wiki/Plugins:LightwireIntegration.cfm) or any other custom object factory via our [IOC](http://wiki.coldbox.org/wiki/Plugins:IOC.cfm) plugin. 

###Dependency Injection

![](EventHandlerInjection.jpg)