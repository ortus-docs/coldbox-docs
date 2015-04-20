# Standard Return Types

The [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) has one setting that affects proxy operation:

* ProxyReturnCollection : This boolean setting determines if the proxy should return what the event handlers return or just return the request collection structure every time a process() method is called. This can be a very useful setting, if you don't even want to return any data from the handlers (via the process() method). The framework will just always return the request collection structure back to the proxy caller. By default, the framework has this setting turned to false so you can have more control and flexibility.

