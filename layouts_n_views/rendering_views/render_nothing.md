# Render Nothing

You can also tell the renderer to not render back anything to the user by using the `event.noRender()` method.. Maybe you just took some input and need to gracefully shutdown the request into the infamous white screen of death.

```js
component name="general"{

	function saveData(event,rc,prc){
		// do your work here …..

		// set for no render
		event.noRender();
	}

}
```

