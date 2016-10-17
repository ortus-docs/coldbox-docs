# Life-Cycle Events

![](.././images/testing-lifecycle.png)

ColdBox testing leverages TestBox's testing life-cycle events (http://testbox.ortusbooks.com/content/life-cycle_methods/index.html) in order to prepare the virtual ColdBox application, request context and then destroy it. For performance, a virtual application is loaded for all test cases contained within a test bundle CFC via the `beforeAll()` and destroyed under `afterAll()`.

```js
function beforeAll(){
	super.beforeAll();
	// do your own stuff here
}

function afterAll(){
	// do your own stuff here
	super.afterAll();
}
```

> **Important** If you override any of these methods and do not funnel the super call, then you might get cached or unexpected results. 

