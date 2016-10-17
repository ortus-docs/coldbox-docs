# Event Driven Programming

However, we went a step further with ColdBox interceptors and created the hooks necessary in order to implement an event-driven programming pattern into the entire interceptor service. Ok ok, what does this mean? It means, that you are not restricted to the pre-defined interception points that ColdBox provides, you can create your own WOW! Really? Yes, you can very easily declare execution points via the configuration file or register at runtime, create your interceptors with the execution point you declared (Conventions baby!!) and then just announce interceptions in your code via the interception API.

![](eventdriven.jpg)

If you are familiar with design patterns, custom interceptors can give you an implementation of observer/observable objects, much like any event-driven system can provide you. If you are not familiar with event-driven systems or what an observer is, please read the following resources. In a nutshell, an observer is an object that is registered to listen for certain types of events, let's say as an example onError is a custom interception point and we create a CFC that has this onError method. Whenever in your application you announce or broadcast that an event of type onError occurred, this CFC will be called by the ColdBox interceptor service.

