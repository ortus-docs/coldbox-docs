# ColdBox Exception Handling

ColdBox provides you with several ways to handle different types of exceptions during a typical ColdBox request execution:

* Global exception handler
* Global onException interceptor
* Global invalid event handler
* Global onInvalidEvent interceptor
* Global missing template handler
* Handler `onMissingAction()`
* Handler `onError()`

## Global Exception Handler

The global exception handler will manage any runtime exception that occurs during the flow of a typical ColdBox request execution. This could be an exception at the handler, model, or view levels. This feature is activated by configuring the `coldbox.exceptionhandler` setting in your configuration `ColdBox.cfc`. The value of the setting is the event that will act as your global exception handler.

```js
coldbox = {
	...
	exceptionHandler = "main.onException"
	...
};
```

This event can the be used for logging the exception, relocating to a fail page or aborting the request. By default once the exception handler executes, ColdBox will continue the request by presenting the user the default ColdBox error page or a customized error template of your choosing via the `coldbox.customErrorTemplate` setting. ColdBox will also place an object in the **private** request collection called `exception`. This object models the entire exception and request data that created the exception. You can then use it to get any information needed to handle the exception.

```js
function onException(event,rc,prc){
	// Log the exception via LogBox
	log.error( prc.exception.getMessage() & prc.exception.getDetail(), prc.exception.getMemento() );

	// Flash where the exception occurred
	flash.put("exceptionURL", event.getCurrentRoutedURL() );

	// Relocate to fail page
	setNextEvent("main.fail");
}
```

> **Caution** Please note that if you set a view for rendering or renderdata in this exception handler, nothing will be rendered. The framework is in exception mode and will not allow any custom renderings or views as that could also produce exceptions as well.

##Global onException Interceptor

This is a standard ColdBox interception point that can be used to intercept whenever a global exception has occurred in the system. The interceptor call is actually made before the global exception handler, any automatic logging or presentation of the error page to the user. This is the first line of defense towards exceptions. You might be asking yourself, what is the difference between writing an `onException()` interceptor and just a simple exception handler? Well, the difference is in the nature of the design. 

Interceptors are designed to be decoupled classes that can react to announced events, thus an event-driven approach. You can have as many CFCs listening to the onException event and react accordingly without them ever knowing about each other and doing one job and one job only. This is a much more flexible and decoupled approach than calling a single event handler where you will procedurally decide what happens in an exception.
component extends="coldbox.system.Interceptor"{
	
	function onException(event, interceptData){
		// Get the exception
		var exception = arguments.interceptData.exception;

		// Do some logging only for some type of error and relocate
		if( exception.type eq "myType" ){
			log.error( exception.message & exception.detail, exception );
			// relocate
			setNextEvent( "page.invalidSave" );
		}
	}
}
Also remember that you need to register the interceptor in your ConfigurationCFC so ColdBox knows about it:
interceptors = [
	
	// Register exception handler
	{ class = "interceptors.ExceptionHandler", properties = {} }

];
Important: Remember that the onException() interceptors execute before the global exception handler, any automatic error logging and presentation of the core or custom error templates.