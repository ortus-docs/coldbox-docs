# Composed Properties

It is imperative that you realize that there is a great object model behind every event handler controller that will enable you to do your work more efficiently. The following are the composed properties every event handler has in their <code>variables</code> scope, you do not need to do anything to retrieve them, they are already there.  :)

![Event Handlers](/images/EventHandlers.jpg)

* **cachebox** : A reference to the CacheBox library (<code>coldbox.system.cache.CacheFactory</code>)
* **controller** : A reference to the Application Controller (<code>coldbox.system.web.Controller</code>)
* **flash** : A reference to the Application's Flash implementation. (<code>coldbox.system.web.flash.AbstractFlashScope</code>)
* **LogBox** : A reference to the application LogBox (<code>coldbox.system.logging.LogBox</code>)
* **Log** : A pre-configured logging logger object (<code>coldbox.system.logging.Logger</code>)
* **wirebox** : A reference to the application WireBox Injector (<code>coldbox.system.ioc.Injector</code>)
* **$super** : A reference to the virtual super class (<code>coldbox.system.EventHandler</code>) Only if using the non-inheritance approach

## Logging
Logging is an important aspect of any application as it allows you to report textual information about your application's state or environment. ColdBox has powerful logging capabilities via LogBox and your event handlers are already configured to use it. Really, just use it. The <code>log</code> object is LogBox logger already configured for your event handler and you can use it for logging:

**Logger Interface**
```js
function debug(message,extrainfo){}
function info(message,extrainfo){}
function warn(message,extrainfo){}
function error(message,extrainfo){}
function fatal(message,extrainfo){}
boolean function canDebug(){}
boolean function canInfo(){}
boolean function canWarn(){}
boolean function canError(){}
boolean function canFatal(){}
```

As you can see, ColdBox tackles all the complexities of logging and gives you so much more than plain 'ol cflog.

```js
function index(event,rc,prc){
	log.info("Hey, I am here executing #event.getCurrentEvent()#", getHTTPRequestData() );
}
```
