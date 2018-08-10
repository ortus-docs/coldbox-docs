# Layouts & Views

ColdBox provides you with a very simple but flexible and powerful layout manager and content renderer. You no longer need to create module tags or convoluted broken up HTML anymore. You can concentrate on the big picture and create as many layouts as your application needs. Then you can programmatically change rendering schemas \(or skinning\) and also create composite or component based views.

In this section we will explore the different rendering mechanisms that ColdBox offers and also how to utilize them. As you know, [event handlers](../event-handlers/) are our controller layer in ColdBox and we will explore how these objects can interact with the user in order to render content, whether HTML, JSON, XML or any type of rendering data.

{% hint style="info" %}
Please note that you can use ColdBox as a pure API solution with modern JavaScript frameworks for the front end like VueJS, Reactor, Angular, etc.
{% endhint %}

## Conventions

Let's do a recap of our conventions for layouts and view locations:

```javascript
+ application
  + layouts
  + views
```

## ColdBox Renderer

![](https://raw.githubusercontent.com/ortus-docs/coldbox-docs/master/full/images/ColdBoxMajorClasses.jpg)

It is imperative to know who does the rendering in ColdBox and that is the **Renderer** class that you can see from our diagram above. As you can tell from the diagram, it includes your layouts and/or views into itself in order to render out content. So by this association and inheritance all layouts and views have some variables and methods at their disposal since they get absorbed into the object. You can visit the [API docs](http://apidocs.ortussolutions.com/coldbox/current) to learn about all the Renderer methods. All of the following property members exist in all layouts and views rendered by the **Renderer**:

| **Property** | **Description** |
| :--- | :--- |
| `event` | A reference to the Request Context object |
| `rc` | A reference to the request collection inside of the request context \(For convenience\) |
| `prc` | A reference to the private request collection inside of the request context \(For convenience\) |
| `html` | A reference to the [HTML Helper](../../digging-deeper/html-helper.md)  that can help you build interactive and safe HTML |
| `cacheBox` | A reference to the [CacheBox](http://wiki.coldbox.org/wiki/CacheBox.cfm) framework factory \(_coldbox.system.cache.CacheFactory_\) |
| `controller` | A reference to the application's ColdBox Controller \(_coldbox.system.web.Controller_\) |
| `flash` | A reference to the current configured Flash Object Implementation that inherits from the AbstractFlashScope [AbstractFlashScope](http://www.coldbox.org/api) \(derived _coldbox.system.web.flash.AbstractFlashScope_\) |
| `logbox` | The reference to the [LogBox](http://wiki.coldbox.org/wiki/LogBox.cfm) library \(coldbox.system.logging.LogBox\) |
| `log` | A pre-configured LogBox [Logger](http://wiki.coldbox.org/wiki/LogBox.cfm) object for this specific class object \(_coldbox.system.logging.Logger_\) |
| `wirebox` | A reference to the [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) object factory \(_coldbox.system.ioc.Injector_\) |

As you can see, all views and layouts have direct reference to the request collections so it makes it incredibly easy to get and put data into it. Also, remember that the Renderer inherits from the Framework SuperType so all methods are at your disposal if needed.

