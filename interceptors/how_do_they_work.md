# How do they work?

Interceptors are CFC's that can either extend ColdBox base class: `coldbox.system.Interceptor`, implement a configuration method called `configure()` and then create as many methods as events it will listen to. All interceptors are treated as **singletons** in your application, so make sure they are thread safe and var scoped.  

![](../images/ColdBoxMajorClasses.jpg)


## Registration
Interceptors can be declared in your configuration file `Coldbox.cfc` under the `interceptors` struct or they can be registered manually via the system's interceptor service.

```js
interceptors = [
    { 
        class   = "cfc.path",
        name    = "unique.name + wirebox ID",
        properties = { 
            //configuraiton struct
        }
]
```

## `configure()`
The interceptor has one important method that you can use for configuration, called `configure()`. This method will be called right after the interceptor gets created and injected with your configuration file properties. 

> **Info** These properties are local to the interceptor only!

As you can see on the diagram, the interceptor class is part of the ColdBox framework super type family, and thus inheriting the functionality of the framework.

```js
/**
* My Interceptor
*/
component extends="coldbox.system.Interceptor"{
	
	function configure(){}

	function preProcess(event, interceptData, buffer){}
}
```

