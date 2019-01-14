# Rendering Views

We have now seen how to set views to be rendered from our handlers. However, we can use some cool methods to render views and layouts on-demand. These methods exist in the **Renderer** and several facade methods exist in the super type so you can call it from any handler, interceptor, view or layout. 

1. `renderView()`
2. `renderExternalView()`
3. `renderLayout()`

Check out the latest [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for the latest arguments:

```javascript
<cfoutput>
// render inline
#renderView( 'tags/metadata' )#

// render from a module
#renderView( view="security/user", module="security" )#

// render and cache
#renderView(
    view="tags/longRendering", 
    cache=true, 
    cacheTimeout=5, 
    cacheProvider="couchbase"
)#

// render a view from the handler action
function showData(event,rc,prc){
    // data here
    return renderView( "general/showData" );    
}

// render an email body content in an email template layout
body = renderlayout( layout='email',view='templates/email_generic' );
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

This will inject a [provider](https://wirebox.ortusbooks.com/advanced-topics/providers) of a **Renderer** into your model objects.  Remember that renderers are transient objects so you cannot treat them as singletons.  The provider is a proxy to the transient object, but you can use it just like the normal object:

```javascript
function sendEmail(){
    
    // code here.
    var body = renderer.renderView( "templates/email" );

}
```

