# Testing Without Virtual Application

The `BaseTestCase` leverages an internal virtual ColdBox application so you can do integration testing. Meaning that whenever you extend from the `BaseTestCase` the virtual ColdBox will be loaded.  However,  you can tell the testing framework to **NOT** load it by using the `this.loadColdBox` variable:

```javascript
component extends="coldbox.system.testing.BaseTestCase"{
		// Do not load the Virtual Application
		this.loadColdBox = false;
}
```

This is a great approach if you still want to leverage the base test case for unit testing but without loading the entire virtual application.

