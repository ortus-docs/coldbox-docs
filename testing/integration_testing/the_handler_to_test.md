# The Handler To Test

Let's use a sample event handler so we can test it:

```js
component extends="coldbox.system.EventHandler"{

	// Default Action
	function index(event,rc,prc){
		prc.welcomeMessage = "Welcome to ColdBox!";
		event.setView("main/index");
	}

	// Do something
	function doSomething(event,rc,prc){
		setNextEvent("main.index");
	}

	/************************************** IMPLICIT ACTIONS *********************************************/

	function onAppInit(event,rc,prc){

	}

	function onRequestStart(event,rc,prc){

	}

	function onRequestEnd(event,rc,prc){

	}

	function onSessionStart(event,rc,prc){

	}

	function onSessionEnd(event,rc,prc){
		var sessionScope = event.getValue("sessionReference");
		var applicationScope = event.getValue("applicationReference");
	}

	function onException(event,rc,prc){
		//Grab Exception From private request collection, placed by ColdBox Exception Handling
		var exception = prc.exception;
		//Place exception handler below:

	}

	function onMissingTemplate(event,rc,prc){
		//Grab missingTemplate From request collection, placed by ColdBox
		var missingTemplate = event.getValue("missingTemplate");

	}

}
```

## Mocking Relocations

I can test this entire handler without me building any views yet. I can even test the relocations that happen via `setNextEvent()`. ColdBox will wire itself up with some mocking classes to intercept those relocations for you and place those values in the request collection for you so you can assert them. It creates a key called setnextevent in the request collection and any arguments passed to the method are also saved as keys with the following pattern:

```js
setnextevent_{argumentName}
```

> **Caution** Any relocation produced by the framework via the `setNextEvent` method will produce some variables in the request collection for you to verify relocations. 

