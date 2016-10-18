# Inline Rendering

We have now seen how to set views to be rendered from our handlers. However, we can use three cool methods to render views and layouts on-demand. These methods exist in the Renderer and several facade methods exist in the super type so you can call it from any handler, interceptor, view or layout.  If you need rendering capabilities in your model layer, we suggest using the following injection: `property name="renderer" inject="provider:coldbox:renderer";`

1. `renderView()`
2. `renderExternalView()`
3. `renderLayout()`

Check out the latest [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for the latest arguments:

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

