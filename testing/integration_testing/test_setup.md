# Test Setup

Now that we know how to start, here is the code for a test. Please note that if you override the setup() method, you must call its parent. If not, the application will not load.

```js
component extends="coldbox.system.testing.BaseTestCase" appMapping="/apps/MyApp"{
	
	function setup(){
		super.setup();
	}

}
```

