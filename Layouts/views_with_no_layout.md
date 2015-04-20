# Views With No Layout

So what happens if I DO NOT want the view to be rendered within a layout? Am I doomed? Of course not, just use the same method with some extra parameters or event.noLayout():

```js
component name="general"{

	function index(event,rc,prc){
	
		// call some model for data and put into the request collection
		prc.myQuery = getModel('MyService').getData();
		// set the view for rendering
		event.setView(view="general/index",noLayout=true);
	
	}

	function index(event,rc,prc){
	
		// call some model for data and put into the request collection
		prc.myQuery = getModel('MyService').getData();
		// set the view for rendering
		event.setView(view="general/index").noLayout();
	
	}

}
```

That's it folks! You use the noLayout=true argument or the noLayout() method.

