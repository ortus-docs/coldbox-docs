# ColdBox Exception Handling

ColdBox provides you with several ways to handle different types of exceptions during a typical ColdBox request execution:

* Global exception handler
* Global onException interceptor
* Global invalid event handler
* Global onInvalidEvent interceptor
* Global missing template handler
* Handler `onMissingAction()`
* Handler `onError()`
* Handler `onInvalidHTTPMethod`

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

Interceptors are designed to be decoupled classes that can react to announced events, thus an event-driven approach. You can have as many CFCs listening to the `onException` event and react accordingly without them ever knowing about each other and doing one job and one job only. This is a much more flexible and decoupled approach than calling a single event handler where you will procedurally decide what happens in an exception.

```js
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
```

Also remember that you need to register the interceptor in your configuration file or dynamically so ColdBox knows about it:

```js
interceptors = [
	
	// Register exception handler
	{ class = "interceptors.ExceptionHandler", properties = {} }

];
```

> **Caution** Remember that the `onException()` interceptors execute before the global exception handler, any automatic error logging and presentation of the core or custom error templates.



## Global Invalid Event Handler

The global invalid event handler allows you to configure an event to execute whenever ColdBox detects that the requested event does not exist. This is a great way to present the user with page not found exceptions and 404 error codes. The setting is called `coldbox.onInvalidEvent` and can be set in your configuration `ColdBox.cfc`. The value of the setting is the event that will handle these missing events.

```js
coldbox = {
	...
	onInvalidEvent = "main.pageNotFound"
	...
};
```

Please note the importance of setting a 404 header as this identifies to the requester that the requested event does not exist in your system. Also note that if you do not set a view for rendering or render data back, the request might most likely fail as it will try to render the implicit view according to the incoming event. 

ColdBox will also place the invalid event requested in the **private** request collection with the value of `invalidevent`.

```js
function pageNotFound(event,rc,prc){
	// Log a warning
	log.warning( "Invalid page detected: #prc.invalidEvent#");

	// Do a quick page not found and 404 error
	event.renderData( data="<h1>Page Not Found</h1>", statusCode=404 );

	// Set a page for rendering and a 404 header
	event.setView( "main/pageNotFound" ).setHTTPHeader( "404", "Page Not Found" );
}
```


## Global `onInvalidEvent` Interceptor

This is a standard ColdBox interception point that can be used to intercept whenever a requested event in your application was invalid. You might be asking yourself, what is the difference between writing an `onInvalidEvent()` interceptor and just a simple invalid event handler? Well, the difference is in the nature of the design. 

Interceptors are designed to be decoupled classes that can react to announced events, thus an event-driven approach. You can have as many CFCs listening to the `onInvalidEvent` event and react accordingly without them ever knowing about each other and doing one job and one job only. This is a much more flexible and decoupled approach than calling a single event handler where you will procedurally decide what happens in an invalid event.

The `interceptData` argument receives the following variables:

* `invalidEvent` : The invalid event string
* `ehBean` : The internal ColdBox event handler bean
* `override` : A boolean indicator that you overwrote the event


You must tell ColdBox that you want to override the invalid event (`override = true`) and you must set in the `ehBean` to tell ColdBox what event to execute:

```js
component extends="coldbox.system.Interceptor"{
	
	function onInvalidEvent(event, interceptData){
		// Log a warning
		log.warning( "Invalid page detected: #arguments.interceptData.invalidEvent#");

		// Set the invalid event to run
		arguments.interceptData.ehBean
		    .setHandler("Main")
		    .setMethod("pageNotFound");

		// Override
		arguments.interceptData.override = true;
	}
}
```

Also remember that you need to register the interceptor in your configuration file or dynamically so ColdBox knows about it:

```js
interceptors = [
	
	// Register invalid event handler
	{ class = "interceptors.InvalidHandler", properties = {} }
	
];
```


##Global Missing Template Handler

The global missing template handler allows you to configure an event to execute whenever ColdBox detects a request to a non-existent CFML page. This is a great way to present the user with page not found exceptions or actually use it to route the request in a dynamic matter, just like if those pages existed on disk. The setting is called `coldbox.missingTemplateHandler` and can be set in your configuration `ColdBox.cfc`. The value of the setting is the event that will handle these missing pages. 

> **Info** Note that in order for this functionality to work the method `onMissingTemplate()` must exist in the `Application.cfc` with the default ColdBox handler code.

```js
coldbox = {
	...
	missingTemplateHandler = "main.missingTemplate"
	...
};
```

ColdBox will place the path to the requested page as a request collection variable called `missingTemplate`, which you can parse and route or just use.

```js
function missingTemplate(event,rc,prc){
	// Log a warning
	log.warning( "Missing page detected: #rc.missingTemplate#");

	// Do a quick page not found and 404 error
	event.renderData( data="<h1>Page Not Found</h1>", statusCode=404 );

	// Set a page for rendering and a 404 header
	event.setView( "main/pageNotFound" ).setHTTPHeader( "404", "Page Not Found" );
}
```



## Handler `onMissingAction()`

This approach allows you to intercept at the handler level when someone requested an action (method) that does not exist in the specified handler. This is really useful when you want to respond to dynamic requests like `/page/contact-us, /page/hello`, where page points to a `Page.cfc` and the rest of the URL will try to match to an action that does not exist. You can then use that portion of the URL to lookup a dynamic record. However, you can also use it to detect when invalid actions are sent to a specific handler.

```js
function onMissingAction(event,rc,prc,missingAction,eventArguments){
	
	// lookup the missing action in our mini cms
	prc.page = pageService.findBySlug( arguments.missingAction );

	if( !prc.page.isPersisted() ){
		prc.missingPage = arguments.missingAction;
		event.setView( "page/notFound" );
	}

	// Else present the page in a dynamic layout
	event.setView( view="page/display", layout=prc.page.getLayout() );

}
```

## Handler `onError()`

This approach allows you to intercept at the handler level whenever a runtime exception has ocurred. This is a great approach when creating a family of event handlers and you create a base handler with the `onError()` defined in it. We have found tremendous success with this approach when building ColdBox RESTFul services in order to provide uniformity for all RESTFul handlers.

> **Caution*  Please note that this only traps runtime exceptions, compiler exceptions will bubble up to a global exception handler or interceptor.

```js
// error uniformity for resources
function onError(event,rc,prc,faultaction,exception){
	prc.response = getModel("ResponseObject");
	
	// setup error response
	prc.response.setError(true);
	prc.response.addMessage("Error executing resource #arguments.exception.message#");
	
	// log exception
	log.error( "The action: #arguments.faultaction# failed when requesting resource: #arguments.event.getCurrentRoutedURL()#", getHTTPRequestData() );
	
	// display
	arguments.event.setHTTPHeader(statusCode="500",statusText="Error executing resource #arguments.exception.message#")
		.renderData( data=prc.response.getDataPacket(), type="json" );
}
```

## Handler `onInvalidHTTPMethod()`

This approach allows you to intercept at the handler level whenever an action execution is requested with an invalid HTTP verb. This is a great approach when creating a family of event handlers and you create a base handler with the `onInvalidHTTPMethod()` defined in it. We have found tremendous success with this approach when building ColdBox RESTFul services in order to provide uniformity for all RESTFul handlers.


```js
/**
* on invalid http verbs
*/
function onInvalidHTTPMethod( event, rc, prc, faultAction, eventArguments ){
	// Log Locally
	log.warn( "InvalidHTTPMethod Execution of (#arguments.faultAction#): #event.getHTTPMethod()#", getHTTPRequestData() );
	// Setup Response
	prc.response = getModel( "Response" )
		.setError( true )
		.setErrorCode( 405 )
		.addMessage( "InvalidHTTPMethod Execution of (#arguments.faultAction#): #event.getHTTPMethod()#" )
		.setStatusCode( 405 )
		.setStatusText( "Invalid HTTP Method" );
	// Render Error Out
	event.renderData( 
		type		= prc.response.getFormat(),
		data 		= prc.response.getDataPacket(),
		contentType = prc.response.getContentType(),
		statusCode 	= prc.response.getStatusCode(),
		statusText 	= prc.response.getStatusText(),
		location 	= prc.response.getLocation(),
		isBinary 	= prc.response.getBinary()
	);
}
```
