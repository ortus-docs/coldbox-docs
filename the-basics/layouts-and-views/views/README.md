# Views

Views are HTML content that can be rendered inside a layout or by themselves. They can be rendered on-demand or set by an event handler. Views can also produce content apart from HTML, like JSON/XML/WDDX, via our view renderer, which we will discover. So get ready for some rendering goodness!

## Setting Views For Rendering - `setView()`

Event handlers are usually the ones responsible for setting views for rendering. However, it's important to understand that ANY object with access to the request context object can also perform this task. This knowledge will keep you informed about the rendering process. The `setView()` method in the request context object is the tool that enables this functionality.

```cfscript
/**
 * Set the view to render in this request. Private Request Collection Name: currentView, currentLayout
 *
 * @view                   The name of the view to set. If a layout has been defined it will assign it, else if will assign the default layout. No extension please
 * @args                   An optional set of arguments that will be available when the view is rendered
 * @layout                 You can override the rendering layout of this setView() call if you want to. Else it defaults to implicit resolution or another override.
 * @module                 The explicit module view
 * @noLayout               Boolean flag, wether the view sent in will be using a layout or not. Default is false. Uses a pre set layout or the default layout.
 * @cache                  True if you want to cache the rendered view.
 * @cacheTimeout           The cache timeout in minutes
 * @cacheLastAccessTimeout The last access timeout in minutes
 * @cacheSuffix            Add a cache suffix to the view cache entry. Great for multi-domain caching or i18n caching.
 * @cacheProvider          The cache provider you want to use for storing the rendered view. By default we use the 'template' cache provider
 * @name                   This triggers a rendering region.  This will be the unique name in the request for specifying a rendering region, you can then render it by passing the unique name to the view();
 *
 * @return RequestContext
 */
function setView(
 view,
 struct args = {},
 layout,
 module                 = "",
 boolean noLayout       = false,
 boolean cache          = false,
 cacheTimeout           = "",
 cacheLastAccessTimeout = "",
 cacheSuffix            = "",
 cacheProvider          = "template",
 name
)
```

{% hint style="info" %}
Setting a view does not mean that it gets rendered immediately. This means that it is deposited in the context of the request. Later on in the execution process, the framework will pick those variables up and do the actual rendering. To do immediate rendering, you will use the inline rendering methods described later.
{% endhint %}

{% code title="handlers/main.cfc" %}
```javascript
component
{

    function index(event,rc,prc){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();
        // set the view for rendering
        event.setView( "general/index" );

    }

}
```
{% endcode %}

We use the `setView()` method to set the view `views/general/index.cfm` to be rendered. The cool thing about this is that we can override the view to be rendered anytime during the request flow. So, the last process to execute the `setView()` method is the one that counts. Also, notice a few things:

* No extension is needed.
* You can traverse directories by using `/` like normal `cfinclude` notation.
* The view can exist in the conventions directory `views` or your configured external locations
* You did not specify a layout for the view, so the application's default layout (`Main`) will be used.

{% hint style="danger" %}
It is best practice that view locations should simulate the event. So, if the event is **general.index**, there should be a **general** folder in the root **views** folder with a view called **index.cfm/bxm**
{% endhint %}

**Let's look at the view code:**

{% code title="main/index.cfm" %}
```javascript
<cfoutput>
<h1>My Cool Data</h1>
#html.table( data=prc.myQuery, class="table table-striped table-hover" )#
</cfoutput>
```
{% endcode %}

I am using our cool HTML Helper class, which is smart enough to render tables, data, HTML 5 elements, etc., and even bind to ColdFusion ORM entities.

### Views With No Layout

So what happens if I DO NOT want the view rendered within a layout? Am I doomed? Of course not, use the same method with the `noLayout` argument or `event.noLayout()` method:

```javascript
component{

    function index(event,rc,prc){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();
        // set the view for rendering
        event.setView( view="general/index", noLayout=true );
    }

    function index(event,rc,prc){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();
        // set the view for rendering
        event.setView( "general/index" ).noLayout();
    }
}
```

### Views With Layouts

If you need the view to be rendered in a **specific** layout, then use the `layout` argument or the `setLayout()` method:

```javascript
component name="general"{

    function index(event,rc,prc){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();
        // set the view for rendering
        event.setView( view="general/index", layout="Ajax" );
    }

    function index(event,rc,prc){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();
        // set the view for rendering
        event.setView( "general/index" ).setLayout( "Ajax" );
    }

}
```

### Views From Modules

If you need the set a view to be rendered from a specific ColdBox Module then use the `module` argument alongside any other argument combination:

```javascript
component name="general"{

    function index( event, rc, prc ){

        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();
        // set the view for rendering
        event.setView( view="general/index", module="shared-views" );

    }

}
```

### View Regions

The `setView()` method also has a `name` argument, which you can use to denote a rendering region.  This will not affect the main set view rendered by the framework.  This will set up the arguments to render the view, and then YOU will use the `#view( name : "myRegion" )#` code to render it wherever you like.  The purpose of this method is to encapsulate rendering mechanics from views and let the handlers control it.

```cfscript
function index( event, rc, prc ){
    ...
    event.setView(
        name : "userLogins",
        args : { data : userService.getLatestLogins() },
        .. Any other view arguments
    )
    ...
}
```

Now that you have set the named region, you can evaluate it and render it it using the `view()`method

```cfscript
<div id="latestLogins">#view( name : "userLogins" )#</div>
```

## Render Nothing

You can also tell the renderer not to render anything back to the user by using the `event.noRender()` method. Maybe you just took some input and need to gracefully shut down the request into the infamous white screen of death.

```javascript
component name="general"{

    function saveData(event,rc,prc){
        // do your work here …..

        // set for no render
        event.noRender();
    }

}
```

## Implicit Views

You can also omit the explicit `event.setView()` if you want, ColdBox will then look for the view according to the executing event's syntax by convention. So if the incoming event is called `general.index` and no view is explicitly defined in your handler, ColdBox will look for a view in the `general` folder called `index.cfm`. We recommend matching event resolution to view resolution, even if you use implicit views.

{% hint style="success" %}
**Tip:** This feature is more for convention purists than anything else. However, we do recommend, as best practice, explicitly declaring the view to be rendered when working with team environments, as everybody will know what happens.
{% endhint %}

```cfscript
component name="general"{

    function index( event, rc, prc ){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance( 'MyService' ).getData();    
    }

}
```

{% hint style="danger" %}
**Caution:** If using implicit views, please note that the **name** of the view will **ALWAYS** be in lowercase. So please be aware of this **limitation**. I would suggest creating URL Mappings with explicit event declarations to control case and location. When using implicit views, you will also lose fine rendering control.
{% endhint %}

### Disabling Implicit Views

You can also disable implicit views by using the `coldbox.implicitViews` configuration setting in your `config/ColdBox.cfc`. This is useful as implicit lookups are time-consuming.

```javascript
coldbox.implicitViews = false;
```

### Case Sensitivity

The ColdBox rendering engine can also be tweaked to use **case-insensitive** or **sensitive** implicit views by using the `coldbox.caseSensitiveImplicitViews` directive in your `config/ColdBox.cfc`. The default is to turn all implicit views to lowercase, so the value is always **false**.

```javascript
coldbox.caseSensitiveImplicitViews = true;
```
