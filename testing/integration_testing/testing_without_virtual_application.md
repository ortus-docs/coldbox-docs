# Testing Without Virtual Application

The `BaseTestCase` leverages an internal virtual ColdBox application so you can do integration testing. Meaning that whenever you extend from the `BaseTestCase` the virtual ColdBox will be loaded. You can tell the testing framework to NOT load it by using the `this.loadColdBox` variable:

```js
component extends="coldbox.system.testing.BaseTestCase"{
	// Do not load the Virtual Application
	this.loadColdBox = false;
}
```

That's it! You will be able to use anything from the BaseTestCase but the virtual application will not be loaded.
