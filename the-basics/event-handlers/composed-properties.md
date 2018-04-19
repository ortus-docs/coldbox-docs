# Composed Properties

It is imperative that you realize that there is a great object model behind every event handler controller that will enable you to do your work more efficiently. The following are the composed properties every event handler has in their `variables` scope, you do not need to do anything to retrieve them, they are already there. :\)

![Event Handlers](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/EventHandlers.jpg)

* **cachebox** : A reference to the CacheBox library \(`coldbox.system.cache.CacheFactory`\)
* **controller** : A reference to the Application Controller \(`coldbox.system.web.Controller`\)
* **flash** : A reference to the Application's Flash implementation. \(`coldbox.system.web.flash.AbstractFlashScope`\)
* **LogBox** : A reference to the application LogBox \(`coldbox.system.logging.LogBox`\)
* **Log** : A pre-configured logging logger object \(`coldbox.system.logging.Logger`\)
* **wirebox** : A reference to the application WireBox Injector \(`coldbox.system.ioc.Injector`\)
* **$super** : A reference to the virtual super class \(`coldbox.system.EventHandler`\) Only if using the non-inheritance approach

## Logging

Logging is an important aspect of any application as it allows you to report textual information about your application's state or environment. ColdBox has powerful logging capabilities via LogBox and your event handlers are already configured to use it. Really, just use it. The `log` object is LogBox logger already configured for your event handler and you can use it for logging:

**Logger Interface**

```javascript
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

```javascript
function index(event,rc,prc){
    log.info("Hey, I am here executing #event.getCurrentEvent()#", getHTTPRequestData() );
}
```

