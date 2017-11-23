# Adding a layout

Every time the framework renders a view, it will try to leverage the default layout which is located in `layouts/Main.cfm` by convention.  This is an HTML file that gives format to your output and contains the location of where the view you wanted should be rendered.  This location is identified by the following code:

```
<div id="maincontent">
#renderView()#
</div>
```

The call to the `renderView()` method with no arguments tells the framework to render the view that was set using `event.setView()`.  This is called a **rendering region**.  You can use many rendering regions within layouts or event within views themselves.

Let's create a new simple layout with two rendering regions.  Open up CommandBox and issue the following commands:

```bash
# Create a Funky layout
coldbox create layout name="Funky"

# Create a footer
coldbox create view name="main/footer"
```

Open the `layouts/Funky.cfm` layout and let's modify it a bit by adding the footer view as a rendering region.

```html
<h1>funky Layout</h1>
<cfoutput>#renderView()#</cfoutput>

<hr>
<cfoutput>#renderView( "main/footer" )#</cfoutput>
```

Now, let's open the handler we created before called `handlers/hello.cfc` and add some code to use our new layout explicitly via adding a `layout` argument to our `setView()` call.


```js

function index( event, rc, prc ){
    // param an incoming variable.
    event.paramValue( "name", "nobody" );
    // set a private variable
    prc.when = dateFormat( now(), "full" );
    
    // set the view to render with our new layout
    event.setView( view="hello/index", layout="Funky" );
}
```

Go execute the event now: `http://localhost:{port}/hello/index` and you will see the view rendered with the words `funky layout` and `footer view` at the bottom.  Eureka, you have now created a layout.

> **Tip** You can also leverage the function `event.setLayout( "Funky" )` to change layouts.

<br>

> **Tip** You can find much more information about layouts and views in our [full docs](/full/layouts_n_views/index.md)

