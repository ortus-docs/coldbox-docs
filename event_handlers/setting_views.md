# Setting Views

The <code>event</code> object is the object that will let you set the views that you want to render, so please explore its API in the CFC Docs. To quickly set a view to render, do the following:

```js
event.setView( 'view' );
```

The view name is the name of the template in the **views** directory without appending the **.cfm**. So if the view is inside another directory you would do this:

```js
event.setView( 'mydirectory/myView' );
```

We recommend that you set your views following the naming convention of your event.  So if your event is **users.index**, your view should be **users/index.cfm**.  This will go a long way with maintainability and consistency.

## Arguments
Here are the arguments for the <code>setView()</code> method:

```js
* @view The name of the view to set. If a layout has been defined it will assign it, else if will assign the default layout. No extension please
* @args An optional set of arguments that will be available when the view is rendered
* @layout You can override the rendering layout of this setView() call if you want to. Else it defaults to implicit resolution or another override.
* @module The explicit module view
* @noLayout Boolean flag, wether the view sent in will be using a layout or not. Default is false. Uses a pre set layout or the default layout.
* @cache True if you want to cache the rendered view.
* @cacheTimeout The cache timeout in minutes
* @cacheLastAccessTimeout The last access timeout in minutes
* @cacheSuffix Add a cache suffix to the view cache entry. Great for multi-domain caching or i18n caching.
* @cacheProvider The cache provider you want to use for storing the rendered view. By default we use the 'template' cache provider
```
