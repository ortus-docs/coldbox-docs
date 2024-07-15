# Rendering Views

## Rendering Methods

We have now seen how to set views to be rendered from our handlers. However, we can use some cool methods to render views and layouts on demand. These methods exist in the **Renderer,** and several facade methods exist in the supertype, so you can call it from any handler, interceptor, view, or layout.

1. `view()`
2. `externalView()`
3. `layout()`

Check out the latest [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for the latest arguments.

### view()

```cfscript
/**
 * Render out a view
 *
 * @view                   The the view to render, if not passed, then we look in the request context for the current set view.
 * @args                   A struct of arguments to pass into the view for rendering, will be available as 'args' in the view.
 * @module                 The module to render the view from explicitly
 * @cache                  Cached the view output or not, defaults to false
 * @cacheTimeout           The time in minutes to cache the view
 * @cacheLastAccessTimeout The time in minutes the view will be removed from cache if idle or requested
 * @cacheSuffix            The suffix to add into the cache entry for this view rendering
 * @cacheProvider          The provider to cache this view in, defaults to 'template'
 * @collection             A collection to use by this Renderer to render the view as many times as the items in the collection (Array or Query)
 * @collectionAs           The name of the collection variable in the partial rendering.  If not passed, we will use the name of the view by convention
 * @collectionStartRow     The start row to limit the collection rendering with
 * @collectionMaxRows      The max rows to iterate over the collection rendering with
 * @collectionDelim        A string to delimit the collection renderings by
 * @prePostExempt          If true, pre/post view interceptors will not be fired. By default they do fire
 * @name                   The name of the rendering region to render out, Usually all arguments are coming from the stored region but you override them using this function's arguments.
 * @viewVariables          A struct of variables to incorporate into the view's variables scope.
 */
function view(
	view                   = "",
	struct args            = getRequestContext().getCurrentViewArgs(),
	module                 = "",
	boolean cache          = false,
	cacheTimeout           = "",
	cacheLastAccessTimeout = "",
	cacheSuffix            = "",
	cacheProvider          = "template",
	collection,
	collectionAs               = "",
	numeric collectionStartRow = "1",
	numeric collectionMaxRows  = 0,
	collectionDelim            = "",
	boolean prePostExempt      = false,
	name,
	viewVariables = {}
)
```

### externalView()

```cfscript
/**
 * Renders an external view anywhere that cfinclude works.
 *
 * @view                   The the view to render
 * @args                   A struct of arguments to pass into the view for rendering, will be available as 'args' iview.
 * @cache                  Cached the view output or not, defaults to false
 * @cacheTimeout           The time in minutes to cache the view
 * @cacheLastAccessTimeout The time in minutes the view will be removed from cache if idle or requested
 * @cacheSuffix            The suffix to add into the cache entry for this view rendering
 * @cacheProvider          The provider to cache this view in, defaults to 'template'
 * @viewVariables          A struct of variables to incorporate into the view's variables scope.
 */
function externalView(
	required view,
	struct args            = getRequestContext().getCurrentViewArgs(),
	boolean cache          = false,
	cacheTimeout           = "",
	cacheLastAccessTimeout = "",
	cacheSuffix            = "",
	cacheProvider          = "template",
	viewVariables          = {}
)
```

### layout()

```cfscript
/**
 * Render a layout or a layout + view combo
 *
 * @layout        The layout to render out
 * @module        The module to explicitly render this layout from
 * @view          The view to render within this layout
 * @args          An optional set of arguments that will be available to this layouts/view rendering ONLY
 * @viewModule    The module to explicitly render the view from
 * @prePostExempt If true, pre/post layout interceptors will not be fired. By default they do fire
 * @viewVariables A struct of variables to incorporate into the view's variables scope.
 */
function layout(
	layout,
	module                = "",
	view                  = "",
	struct args           = getRequestContext().getCurrentViewArgs(),
	viewModule            = "",
	boolean prePostExempt = false,
	viewVariables         = {}
)
```

## Examples

```javascript
<cfoutput>
// render inline
#view( 'tags/metadata' )#

// render from a module
#view( view="security/user", module="security" )#

// render a region
#view( name : "userLogins" )#

// render and cache
#view(
    view="tags/longRendering", 
    cache=true, 
    cacheTimeout=5, 
    cacheProvider="couchbase"
)#

// render a view from the handler action
function showData(event,rc,prc){
    // data here
    return view( "general/showData" );    
}

// render an email body content in an email template layout
body = layout( layout='email',view='templates/email_generic' );
</cfoutput>
```

{% hint style="info" %}
Inline renderings are a great asset for reusing views and doing layout compositions
{% endhint %}

## Model Rendering

If you need rendering capabilities in your model layer, we suggest using the following injection DSL:

```javascript
property name="renderer" inject="provider:coldbox:renderer";
```

This will inject a [provider](https://wirebox.ortusbooks.com/advanced-topics/providers) of a **Renderer** into your model objects. Remember that renderers are transient objects so you cannot treat them as singletons. The provider is a proxy to the transient object, but you can use it just like the normal object:

```javascript
function sendEmail(){
    
    // code here.
    var body = renderer.view( "templates/email" );

}
```
