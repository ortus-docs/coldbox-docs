# Layouts From A Module

If you need the set layout to be rendered from a specific module then use the `module` argument from the `setLayout() or renderLayout()` methods:

```js
component name="general"{

	function index(event,rc,prc){

		// call some model for data and put into the request collection
		prc.myQuery = getInstance('MyService').getData();
		// set the view for rendering
		event.setLayout( name="admin", module="contentbox" );

	}

}
```
