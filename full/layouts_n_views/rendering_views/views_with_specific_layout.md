# Views With Specific Layout

If you need the view to be rendered in a specific layout, then use the layout argument:

```js
component name="general"{

	function index(event,rc,prc){
	
		// call some model for data and put into the request collection
		prc.myQuery = getInstance('MyService').getData();
		// set the view for rendering
		event.setView( view="general/index", layout="Ajax" );
	
	}

}
```

The view will now be rendered into the Ajax layout instead of the default one.
