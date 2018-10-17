# Views

Views are HTML content that can be rendered inside of a layout or by themselves. They can be either rendered on demand or by being set by an event handler. Views can also produce any type of content apart from HTML like JSON/XML/WDDX via our view renderer that we will discover also. So get ready for some rendering goodness!

## Setting Views For Rendering

Usually, event handlers are the objects in charge of setting views for rendering. However, ANY object that has access to the request context object can do this also. This is done by using the `setView()` method in the request context object.

{% hint style="info" %}
Setting a view does not mean that it gets rendered immediately. It means that it is deposited in the request context. The framework will later on in the execution process pick those variables up and do the actual rendering. To do immediate rendering you will use the inline rendering methods describe later on.
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="handlers/main.cfc" %}
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
{% endcode-tabs-item %}
{% endcode-tabs %}

We use the `setView()` method to set the view `views/general/index.cfm` to be rendered. Now the cool thing about this, is that we can override the view to be rendered anytime during the flow of the request. So the last process to execute the `setView()` method is the one that counts. Also notice a few things:

* No `.cfm` extension is needed.
* You can traverse directories by using `/` like normal `cfinclude` notation.
* The view can exist in the conventions directory `views` or in your configured external locations
* You did not specify a layout for the view, so the application's default layout \(`main.cfm`\) will be used.

{% hint style="danger" %}
It is best practice that view locations should simulate the event. So if the event is **general.index**, there should be a **general** folder in the root **views** folder with a view called **index.cfm.**
{% endhint %}

**Let's look at the view code:**

{% code-tabs %}
{% code-tabs-item title="main/index.cfm" %}
```javascript
<cfoutput>
<h1>My Cool Data</h1>
#html.table( data=prc.myQuery, class="table table-striped table-hover" )#

</cfoutput>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

I am using our cool HTML Helper class that is smart enough to render tables, data, HTML 5 elements etc and even bind to ColdFusion ORM entities.

### Views With No Layout

So what happens if I DO NOT want the view to be rendered within a layout? Am I doomed? Of course not, just use the same method with the `noLayout` argument or `event.noLayout()` method:

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

    function index(event,rc,prc){

        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();
        // set the view for rendering
        event.setView( view="general/index", module="shared-views" );

    }

}
```

## Render Nothing

You can also tell the renderer to not render back anything to the user by using the `event.noRender()` method. Maybe you just took some input and need to gracefully shutdown the request into the infamous white screen of death.

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

You can also omit the explicit `event.setView()` if you want, ColdBox will then look for the view according to the executing event's syntax by convention. So if the incoming event is called `general.index` and no view is explicitly defined in your handler, ColdBox will look for a view in the `general` folder called `index.cfm`. That is why we recommend trying to match event resolution to view resolution even if you use or not implicit views.

{% hint style="success" %}
**Tip:** This feature is more for conventions purists than anything else. However, we do recommend as best practice to use explicitly declare the view to be rendered when working with team environments as everybody will know what happens.
{% endhint %}

```javascript
component name="general"{

    function index(event,rc,prc){
        // call some model for data and put into the request collection
        prc.myQuery = getInstance('MyService').getData();    
    }

}
```

{% hint style="danger" %}
**Caution** If using implicit views, please note that the **name** of the view will **ALWAYS** be in lower case. So please be aware of this **limitation**. I would suggest creating URL Mappings with explicit event declarations so case and location can be controlled. When using implicit views you will also loose fine rendering control.
{% endhint %}

### Disabling Implicit Views

You can also disable implicit views by using the `coldbox.implicitViews` configuration setting in your `config/ColdBox.cfc`. This is useful as implicit lookups are time-consuming.

```javascript
coldbox.implicitViews = false;
```

### Case Sensitivity

The ColdBox rendering engine can also be tweaked to use **case-insensitive** or **sensitive** implicit views by using the `coldbox.caseSensitiveImplicitViews` directive in your `config/ColdBox.cfc`. The default is to turn all implicit views to lower case, so the value is always **false**.

```javascript
coldbox.caseSensitiveImplicitViews = true;
```

