# ColdBox Proxy

One of the great tools you will find in the ColdBox Platform is the ColdBox proxy. This proxy element converts this MVC framework into a remote-driven framework. You can use it for integrating Flex/Air/AJAX and Remote calls. In this guide we will only touch AJAX, but the concepts are the same. For an in depth guide, please read the ColdBox Proxy Guide. So let's start with the basics.

### What is proxy?

The proxy is just a simple CFC that you will use and interact with from remote systems like AJAX. You call it or use data binding with it or even use the great ColdFusion Ajax tags: cfajaxproxy with it. It has several utility methods that you can use when dealing with remote calls. Some are shown below, for a full list, please visit the [latest API](http://apidocs.coldbox.org/cbQuickDocs/search/index). I would also recommend you check out the sample application called CF8Ajax in the bundle download. It contains all kinds of AJAX-Coldbox goodness!!

Note : Using the ColdBox Proxy for Ajax calls is not our preferred approach as direct calls to render out HTML or data is preferred. However, there are certain ColdFusion data binding tags or features that require a CFC to connect to. Then the ColdBox proxy will help you. 

Useful Methods (There are more)

|Method|Description|
|--|--|
|process|execute an event remotely. You call this method and pass in an event and any arguments you like. The framework will then simulate an event request and return you data or the entire request collection. This is determined by you by a setting in the configuration file. Your event handlers can now return data instead of setting views to render. The framework morphs into a remote framework.|
|announceInterception |The ability to announce an interception and send in a structure of data. |
|getPlugin|Ability to get a coldbox plugin |
|getBean |Ability to get a bean from an IoC container |
|getModel |Ability to get a model object from [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm)|
|tracer|Ability to trace messages to the debugger |
|getColdboxOCM |Get a reference to ColdBox caches|

