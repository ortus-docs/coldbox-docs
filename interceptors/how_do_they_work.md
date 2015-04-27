# How do they work?

Interceptors are CFC's that can either extend or not our ColdBox base class: coldbox.system.Interceptor, implement a configuration method called configure and then create as many methods as events it will listen to. All interceptors are treated as singletons in your application, so make sure they are thread safe and var scoped.

![](../images/ColdBoxMajorClasses.jpg)

The interceptor has one important method that you can use for configuration, called `configure()`. This method will be called right after the interceptor gets created and injected with your configuration file properties. 

> **Info** These properties are local to the interceptor only!

As you can see on the diagram, the interceptor class is part of the ColdBox framework super type family, and thus inheriting the functionality of the framework. (See API).

```js
/**
* My Interceptor
*/
component{
	
	function configure(){}

	function preProcess(event, interceptData, buffer){}
}
```

