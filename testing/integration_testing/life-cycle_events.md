# Life-Cycle Events

![](../../images/testing-lifecycle.png)

ColdBox testing leverages TestBox's testing life-cycle events (http://testbox.ortusbooks.com/content/life-cycle_methods/index.html) in order to prepare the virtual ColdBox application, request context and then destroy it. For performance, a virtual application is loaded for all test cases contained within a test bundle CFC. If you want to override any of these methods: `setup(), afterTests(), beforeTests()` please remember to call super on them to execute them:

```js

function beforeTests(){
	// my before stuff

	// call super
	super.beforeTests();
}

function setup(){
	// my setup stuff

	// call super
	super.setup();
}

function afterTests(){
	// my after tests stuff

	super.afterTests();
}
```

> **Important** If you override any of these methods and do not funnel the super call, then you might get cached or unexpected results. 

