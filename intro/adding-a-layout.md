# Adding a layout

Every time the framework renders a view, it will try to leverage the default layout which is located in `layouts/Main.cfm` by convetion.  This is an HTML file that gives format to your output and contains the location of where the view you wanted should be rendered.  This location is identified by the following code:

```
<div id="maincontent">
#renderView()#
</div>
```

The call to the `renderView()` method with no arguments tells the framework to render the view that was set using `event.setView()`.  This is called a rendering region.  You can use many rendering regions which typically replace `cfincludes`. 

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
#renderView( "main/footer" )#
```