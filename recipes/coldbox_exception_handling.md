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

This event can the be used for logging the exception, relocating to a fail page or aborting the request. By default once the exception handler executes, ColdBox will continue the request by presenting the user the default ColdBox error page or a customized error template of your choosing via the `coldbox.customErrorTemplate` setting. ColdBox will also place an object in the request collection called exceptionBean. This object models the entire exception and request data that created the exception. You can then use it to get any information needed to handle the exception.
