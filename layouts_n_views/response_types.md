# Response Types

The event handlers will be the objects in question that will be responding to user requests and they have a few choices when it comes down to rendering content back:

* Set a view to be rendered back to the user (with or without a layout) 
* Render data in multiple view formats: JSON, XML, WDDX, HTML, TEXT or CUSTOM
* Do nothing or the infamous White Screen of Death
* Do an HTTP redirect to another event via `setNextEvent`

## Conventions

Let's do a recap of our conventions for layouts and view locations:

```js
+ application
  + layouts
  + views
```

All your layouts will go in the `layouts` folder and all your views will go in the `views` folder.

## ColdBox Render

![](LayoutsViewsUML.jpg)

It is imperative to know who does the rendering in ColdBox and that is the Renderer core plugin that you can see from our diagram above. As you can tell from the diagram, it includes your layouts and/or views into itself in order to render out content. So by this association and inheritance all layouts and views have some variables and methods at their disposal since they get absorbed into the plugin. You can visit the API docs to learn about all the Renderer Plugin methods. All of the following property members exist in all layouts and views rendered by the Renderer:


|Property|Description|
|--|--|
|event|A reference to the Request Context|
|rc|A reference to the request collection inside of the request context (For convenience)|
|prc|A reference to the private request collection inside of the request context (For convenience)|
|html|A reference to the [HTML Helper](http://wiki.coldbox.org/wiki/Plugins:HTMLHelper.cfm) plugin (coldbox.system.plugins.HTMLHelper) that can help you build interactive HTML|
|cacheBox|A reference to the [CacheBox](http://wiki.coldbox.org/wiki/CacheBox.cfm) framework factory (*coldbox.system.cache.CacheFactory*)|
|controller|A reference to the application's ColdBox Controller (*coldbox.system.web.Controller*)|
|flash|A reference to the current configured Flash Object Implementation that inherits from the AbstractFlashScope [AbstractFlashScope](http://www.coldbox.org/api) (derived *coldbox.system.web.flash.AbstractFlashScope*)|
|logbox|The reference to the [LogBox](http://wiki.coldbox.org/wiki/LogBox.cfm) library (coldbox.system.logging.LogBox)|
|log|A pre-configured LogBox [Logger](http://wiki.coldbox.org/wiki/LogBox.cfm) object for this specific class object (*coldbox.system.logging.Logger*)|
|wirebox|A reference to the [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) object factory (*coldbox.system.ioc.Injector*)|

As you can see, all views and layouts have direct reference to the request collections so it makes it incredibly easy to get and put data into it. Also, remember that the Renderer inherits from the base ColdBox Plugin class which in turn inherits from the FrameworkSuperType class. So all methods are at your disposal if needed.

> **Important** Do not put any type of business logic in layouts and views, it does not belong there! 

