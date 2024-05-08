# Explicit Module Renderings

You can also explicitly render layouts or views directly from a module via the Renderer plugin's `layout()` and `view()` methods. These methods now can take an extra argument called module.

* `layout(layout, view, module)`
* `view(view, cache, cacheTimeout,cacheLastAccessTimeout,cacheSuffix, module)`

So you can easily pinpoint renderings if needed.

> **Caution** Please note that whenever these methods are called, the override algorithms ALSO are in effect. So please refer back to the view and layout parent lookup properties in your modules' configuration.
