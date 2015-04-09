# Model Integration

We have a complete guide dedicated to Model Integration but we wanted to review a little here since event handlers need to talk to the model layer all the time. By default you can interact with your models from your event handlers in two ways:

*  Dependency Injection
*  Request model objects


ColdBox offers its own dependency injection framework, [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm), which allows you by convention to talk to your model objects. However, ColdBox also allows you to connect to [ColdSpring](http://wiki.coldbox.org/wiki/Plugins:ColdspringIntegration.cfm), [LightWire](http://wiki.coldbox.org/wiki/Plugins:LightwireIntegration.cfm) or any other custom object factory via our [IOC](http://wiki.coldbox.org/wiki/Plugins:IOC.cfm) plugin. 

###Dependency Injection

![](../images/EventHandlerInjection.jpg)

 Your event handlers can be autowired with dependencies from either WireBox, ColdSpring, or any custom object factory by means of our[ injection DSL](wiki.coldbox.org/wiki/WireBox.cfm#Injection_DSL). By autowiring dependencies into event handlers, they will become part of the life span of the event handlers and thus gain on the performance that an event handler is wired with all necessary parts upon creation. This is a huge benefit and we encourage you to use injection whenever possible. Please note that injection [aggregates](http://en.wikipedia.org/wiki/Object_composition) model objects into your event handlers. The [Injection DSL](http://wiki.coldbox.org/wiki/WireBox.cfm) can be applied to: 
 
 * cfproperties
 * constructor arguments
 * setter methods
 
It will be your choice to pick an approach, but we mostly concentrate on property injection as you will see from our examples.

