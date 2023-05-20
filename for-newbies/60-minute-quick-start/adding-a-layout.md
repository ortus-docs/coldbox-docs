# Adding A Layout

Every time the framework renders a view, it will try to leverage the default layout located in `layouts/Main.cfm` by convention. This is a reusable CFML template that gives format to your HTML output and contains the _location_ of where the view you want should be rendered.

{% hint style="success" %}
**Tip :** The request context can also be used to choose a different layout at runtime via the `event.setLayout()` method or the `layout` argument in the `event.setView( layout: "login" )` method.
{% endhint %}

{% hint style="success" %}
**Tip :** The request context can also be used to render a view with NO layout at all via the `event.noLayout()` method or `event.setView( noLayout: true )`
{% endhint %}

## Layout Code

The layout has everything you want to wrap views or other layouts with.  You can use our rendering methods to do inline renderings or tell ColdBox where the set view should render:

* `view()` - Render the set view via `event.setView()`
* `view( name: "toolbar" )` - Render a [named view](../../the-basics/layouts-and-views/views/rendering-views.md)
* `view( "partials/footer" )` - Render a explicit view
* `layout( name )` - Render another layout within this layout

```markup
<div id="maincontent">
#view()#
</div>
```

The call to the `view()` method with no arguments tells the framework to render the view that was set using `event.setView()`. This is called a **rendering region**. You can use as many rendering regions within layouts or views.

{% hint style="info" %}
**Named Regions:** The `setView()` method even allows you to name these regions and then render them in any layout or other views using the `name` argument.
{% endhint %}

## Creating A Layout

Let's create a new simple layout with two rendering regions. Open up CommandBox and issue the following commands:

```bash
# Create a Funky layout
coldbox create layout name="Funky" --open

# Create a footer
coldbox create view name="main/footer" --open
```

Open the `layouts/Funky.cfm` layout and let's modify it a bit by adding the footer view as a rendering region.

```markup
<cfoutput>
<!DOCTYPE html>
<html lang="en">
    <head></head>
    <body>
        <h1>funky Layout</h1>
        <div class="container">
            #view()#
        </div>
        <hr>
        #view( "main/footer" )#
    </body>
</html>
</cfoutput>
```

Now let's do our footer:

```html
<cfoutput>
<small>I am a funky footer generated at #now()# running in #getSetting( 'environment' )#</small>
</cfoutput>
```

### Settings

As you can see from the footer, we introduced a new function called `getSetting()` .  All layouts, handlers, interceptors, and views inherit the [Framework Super Type](../../the-basics/models/super-type-usage-methods.md) functionality.  There are tons of methods inherited from this class that you can use in your application, from getting models to settings, relocating, async computations, and so much more.&#x20;

## Using The Layout

Now, let's open the handler we created before, called `handlers/hello.cfc` and add some code to use our new layout explicitly by adding a `layout` argument to our `setView()` call.

```javascript
function index( event, rc, prc ){
    // param an incoming variable.
    event.paramValue( "name", "nobody" );
    // set a private variable
    prc.when = dateFormat( now(), "full" );

    // set the view to render with our new layout
    event.setView( view="hello/index", layout="Funky" );
    // or we can do this:
    // event.setView( "hello/index" ).setLayout( "Funky" );
}
```

Go execute the event now: `http://localhost:{port}/hello/index` and you will see the view rendered with the words `funky layout` and `footer view` at the bottom. Eureka, you have now created a layout.

{% hint style="info" %}
You can also leverage the function `event.setLayout( "Funky" )` to change layouts and even concatenate the calls:

`event`\
&#x20; `.setView( "hello/index" )`  \
&#x20; `.setLayout( "Funky" );`
{% endhint %}
