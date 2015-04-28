# Life-Cycle Events

ColdBox testing leverages MXUnit's testing life-cycle events in order to prepare the virtual ColdBox application, request context and then destroy it. For performance, a virtual application is loaded for all test cases contained within a test case CFC. If you want to override any of these methods: `setup(), afterTests(), beforeTests()` please remember to call super on them to execute them:

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

