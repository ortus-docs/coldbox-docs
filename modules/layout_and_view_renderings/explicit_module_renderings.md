# Explicit Module Renderings

You can also explicitly render layouts or views directly from a module via the Renderer plugin's `renderLayout()` and `renderView()` methods. These methods now can take an extra argument called module.


* renderLayout(layout, view, module)
* renderView(view, cache, cacheTimeout,cacheLastAccessTimeout,cacheSuffix, module)

