# Layouts From A Module

If you need the set layout to be rendered from a specific [ColdBox Module](http://wiki.coldbox.org/wiki/Modules.cfmhttp://) then use the module argument alongside any other argument combination:

```js
component name="general"{

	function index(event,rc,prc){
	
		// call some model for data and put into the request collection
		prc.myQuery = getModel('MyService').getData();
		// set the view for rendering
		event.setLayout(layout="admin",module="contentbox");
	
	}

}
```

By using the module argument, you are explicitely telling the ColdBox renderer to look for the layout inside of the passed module layout's convention.

