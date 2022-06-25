# Adding A Layout

Every time the framework renders a view, it will try to leverage the default layout which is located in `layouts/Main.cfm` by convention. This is an HTML file that gives format to your output and contains the _location_ of where the view you want should be rendered.

{% hint style="success" %}
**Tip :** The request context can also be used to choose a different layout at runtime via the `event.setLayout()` method or the `layout` argument in the `event.setView()` method.
{% endhint %}

{% hint style="success" %}
**Tip :** The request context can also be used to render a view with NO layout at all via the `event.noLayout()` method.
{% endhint %}

## Layout Code

This _location_ is identified by the following code: `renderView()`

```markup
<div id="maincontent">
#renderView()#
</div>
```

The call to the `renderView()` method with no arguments tells the framework to render the view that was set using `event.setView()`. This is called a **rendering region**. You can use as many rendering regions within layouts or even within views themselves.

{% hint style="info" %}
**Named Regions:** The `setView()` method even allows you to name these regions and then render them in any layout or other views using the `name` argument.
{% endhint %}

## Creating A Layout

Let's create a new simple layout with two rendering regions. Open up CommandBox and issue the following commands:

```bash
# Create a Funky layout
coldbox create layout name="Funky"

# Create a footer
coldbox create view name="main/footer"
```

Open the `layouts/Funky.cfm` layout and let's modify it a bit by adding the footer view as a rendering region.

```markup
<h1>funky Layout</h1>
<cfoutput>#renderView()#</cfoutput>

<hr>
<cfoutput>#renderView( "main/footer" )#</cfoutput>
```

If you are use to using `cfinclude` to reuse templates, think about it the same way.  `renderview()` is a much more powerful `cfinclude.`

## Using The Layout

Now, let's open the handler we created before called `handlers/hello.cfc` and add some code to use our new layout explicitly via adding a `layout` argument to our `setView()` call.

```javascript
function index( event, rc, prc ){
    // param an incoming variable.
    event.paramValue( "name", "nobody" );
    // set a private variable
    prc.when = dateFormat( now(), "full" );

    // set the view to render with our new layout
    event.setView( view="hello/index", layout="Funky" );
}
```

Go execute the event now: `http://localhost:{port}/hello/index` and you will see the view rendered with the words `funky layout` and `footer view` at the bottom. Eureka, you have now created a layout.

{% hint style="info" %}
You can also leverage the function `event.setLayout( "Funky" )` to change layouts and even concatenate the calls:

`event`\
&#x20; `.setView( "hello/index" )`  \
&#x20; `.setLayout( "Funky" );`
{% endhint %}
