# Convention Methods

Every event handler controller has some **special** methods that if you create them, they come alive.  Just like the special methods in <code>Application.cfc</code>

##onMissingAction()

With this convention you can create virtual events that do not even need to be created or exist in a handler. Every time an event requests an action from an event handler and that action does not exists in the handler, the framework will check if an onMissingAction() method has been declared. If it has, it will execute it. This is very similar to ColdFusion's onMissingMethod but on an event-driven framework.


```js
function onMissingAction( event, rc, prc, missingAction, eventArguments ){
}
```