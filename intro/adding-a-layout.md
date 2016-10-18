# Adding a layout

Every time the framework renders a view, it will try to leverage the default layout which is located in `layouts/Main.cfm`.  This is an HTML file that gives format to your output and contains the location of where the view you wanted should be rendered.  This location is identified by the following code:

```
<div id="maincontent">
#renderView()#
</div>
```

The call to the `renderView()` method with no arguments tells the framework to render the view that was set using `event.setView()` from any event handler.  This is called a rendering region.  You can use many rendering regions which typically replace `cfincludes`. 

Let's create a new simple layout with two rendering regions.  Open up CommandBox and issue the following commands:

```bash
# Create a Funky layout
coldbox create layout name="Funky"

# Create a footer
coldbox create view name="main/footer"
```

