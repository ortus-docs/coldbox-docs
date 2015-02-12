# Convention Methods

Every event handler controller has some **special** methods that if you create them, they come alive.  Just like the special methods in <code>Application.cfc</code>

## onMissingAction()

With this convention you can create virtual events that do not even need to be created or exist in a handler. Every time an event requests an action from an event handler and that action does not exists in the handler, the framework will check if an <code>onMissingAction()<code> method has been declared. If it has, it will execute it. This is very similar to ColdFusion's <code>onMissingMethod()</code> but on an event-driven framework.


```js
function onMissingAction( event, rc, prc, missingAction, eventArguments ){
}
```

This event has an extra argument: **missingAction** which is the missing action that was requested. You can then do any kind of logic against this missing action and decide to do internal processing, error handling or anything you like. The power of this convention method is extraordinary, you have tons of possibilities as you can create virtual events on specific event handlers.

## onError()

This is a localized error handler for your event handler. If any type of runtime error ocurrs in an event handler and this method exists, then the framework will call your onError() method so you can process the error. If the method does not exist, then normal error procedures ensue.
