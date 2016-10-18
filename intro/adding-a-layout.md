# Adding a layout

Every time the framework renders a view, it will try to leverage the default layout which is located in `layouts/Main.cfm`.  This is an HTML file that give format to your output and contains the location of where the view you wanted should be rendered.  This location is identified by the following code:

```
<div id="maincontent">
#renderView()#
</div>
```

The call to the `renderView()` method with no arguments tells the framework to render the view that was set using `event.setView()` from any event handler.

