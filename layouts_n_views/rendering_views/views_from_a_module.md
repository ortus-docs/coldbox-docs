# Views From A Module

If you need the set view to be rendered from a specific ColdBox Module then use the `module` argument alongside any other argument combination:

```js
component name="general"{

	function index(event,rc,prc){
	
		// call some model for data and put into the request collection
		prc.myQuery = getModel('MyService').getData();
		// set the view for rendering
		event.setView( view="general/index", module="shared-views" );
	
	}

}
```

