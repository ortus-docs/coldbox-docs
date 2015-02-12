# Setting Views

The <code>event</code> object is the object that will let you set the views that you want to render, so please explore its API in the CFC Docs. To quickly set a view to render, do the following:

```js
event.setView( 'view' );
```

The view name is the name of the template in the **views** directory without appending the **.cfm**. So if the view is inside another directory you would do this:

```js
event.setView( 'mydirectory/myView' );
```

We recommend that you set your views following the naming convention of your event.  So if your event is **users.index**, your view should be **users/index.cfm**.  This will go a long way with maintainability and consistency and also will activate **implicit views** where you don't event have to use the set view method call.

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


## Cached Views
You can leverage the caching arguments in the <code>setView()</code> method in order to render and cache the output of the views once the framework renders it.  These cached views will be stored in the **template** cache region, which you can retrieve or purge by talking to it: <code>getCache( 'template' )</code>.

```js
// Cache in the template cache for the default amount of time
event.setView( view='myView', cache=true );
// Cache in the template cache for up to 60 minutes, or 20 minutes after the last time it's been used
event.setView( view='myView', cache=true, cacheTimeout=60, cacheLastAccessTimeout=20 );
// Cache a different version of the view for each language the site has
event.setView( view='myView', cache=true, cacheSuffix=prc.language );
```

## View Arguments
Data can be passed from your handler to the view via the **rc** or **prc**. If you want to pass data to a view without polluting **rc** and **prc**, you can pass it directly via the **args** parameter.

```js
var viewData = {
  data1 = service.getData1(),
  data2 = service.getData2()
};

event.setView( view='myView', args=viewData );
```
Access the data in the view like so:

```html
<cfoutput>
  Data 1: #args.data1#<br>
  Data 2: #args.data2#
</cfoutput>
```

## No Rendering
Well, if you don't want to, then you don't have to. The framework gives you a method in the event object that you can use if maybe this specific request should just terminate gracefully and not render anything at all. All you need to do is use the event object to call on the <code>noRender()</code> method.

```
event.noRender();
```






