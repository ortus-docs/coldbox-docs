# Inline Rendering

We have now seen how to set views to be rendered from our handlers. However, we can use three cool methods to render views and layouts on-demand, much how cfinclude is used. These methods exist in the Renderer plugin and several facade methods exist in the super type so you can call it from any plugin, handler, interceptor, view or layout:

#### renderView()

Render inline views from the parent application or from specific modules according to arguments:

|Argument|Type|Required|Default|Description|
|--|--|--|--|--|
|view|string|false|look in request collection |The name of the view to render|
|cache|boolean|false|false|Cache the view to be rendered|
|cacheTimeout|numeric|false|(provider default) |The timeout in minutes or whatever the cache provider defines|
|cacheLastAccessTimeout |numeric|false|(provider default) |The idle timeout in minutes or whatever the cache provider defines|
|cacheSuffix |string|false|---|Adds a suffix key to the cached key. Used for providing uniqueness to the cacheable entry|
|cacheProvider |string|false|*template*|Uses the passed in cache provider instead of the default template cache region|
|module|string|false|---|The name of the module to render the view from|
|args|struct|false|{}|Local arguments to pass into the view rendering|
|collection|any|false|---|A collection to use by this Renderer to render the view as many times as the items in the collection (Array or Query)|
|collectionAs |any|false|---|The name of the collection variable in the partial rendering. If not passed, we will use the name of the view by convention|
|prepostExempt |boolean|false|false|If true, pre/post view interceptors will not be fired. By default they do fire|

#### renderExternalView()

Render inline views from anywhere in the server according to arguments:

|Argument|Type|Required|Default|Description|
|--|--|--|--|--|
|layout|string|true|---|The name of the explicit layout to render|
|view|string|false|---|The name of the view to render this layout with|
|module|string|false|---|The name of the module to render the view from|
|args|struct|false|{}|Local arguments to pass into the view rendering|

Some Examples

```js
<cfoutput>
// render inline
#renderView('tags/metadata')#

// render from a module
#renderView(view="security/user",module="security")#

// render and cache
#renderView(view="tags/longRendering", cache=true, cacheTimeout=5, cacheProvider=Couchbase)#

// render a view from the handler action
function showData(event,rc,prc){
	// data here
	return renderView(view="general/showData");	
}

// render an email body content in an email template layout
body = renderlayout(layout='email',view='templates/email_generic');
</cfoutput>
```

> **Info** Inline renderings are a great asset for reusing views and doing layout compositions

