# Event Handlers

Event handlers are our **controllers** in the MVC design pattern. So every time you hear event handler, you are taking about a controller that can listen to external events or internal events in ColdBox.

<img src="../images/ControllerLayer.jpg">

## CFCs
Event handlers are implemented as CFCs that are responsible for handling requests coming into the application from either a FORM/URL/REST or Remote sources (Flex/Air/SOAP). These event handlers carry the task of controlling your application flow, calling business logic, preparing a display to a user and pretty much controlling flow. 

```js
component extends="coldbox.system.EventHandler"{
}
```

> **Info** : You can also remove the inheritance from the CFC.  However, we highly encourage it as it will give you a faster startup and IDE introspection.

## Method Actions
Every method in this event handler CFC that has an access of `public` is automatically exposed as a runnable event in ColdBox and it will be auto-registered for you. That means there is no extra configuration or XML logic to define them. By convention they become alive once you create them and clients can request them. In ColdBox terms, each of these event handler methods are referred to as **actions**. As you can see from the diagram, ColdBox captures an incoming variable called `event` and uses it to execute the correct event handler CFC and action method.

```js
component{
	function index( event, rc, prc ){
		return "Hi from controller land!";
	}
}
```
### Default Action: index()
The default action of ANY handlers is the method `index()`.  So if you try to execute an event handler without defining the action, ColdBox will look for the method `index()` to execute for you.

> **Danger** : Event Handler's are not to be used to write business logic.  They should be light and fluffy!


## How are events called?
Events are determined via a special variable that can be sent in via the FORM or URL or REMOTELY called `event`.  If no event is detected as an incoming variable, the framework will look in the configuration directives for the `DefaultEvent` and use that instead. If you did not set a `DefaultEvent` setting then the framework will use the following [convention](../configuration/conventions.md) for you: `main.index`

> **Hint** : You can even change the `event` variable name by updating the `EventName` setting in your `coldbox` configuration directive.

Ok, so now that we know how we can determine what event to execute, how do we write the events since they are used by convention?

### Event Syntax
So in order to call them you will use the following event syntax notation format:

```js
event={module:}{package.}{handler}{.action}
```

* **no event** : Default event by convention is `main.index`
* **event={handler}** : Default action method by convention is `index()`
* **event={handler}.{action}** : Explicit handler + action method
* **event={package}.{handler}.{action}** : Packaged notation
* **event={module}:{package}.{handler}.{action}** : Module Notation (See [ColdBox Modules](../modules/index.md))

This looks very similar to a java or CFC method call, example: String.getLength(), but without the parenthesis. Once the event variable is set and detected by the framework, the framework will tokenize the event string to retrieve the CFC and action call and validate it against the internal registry of registered events. It then continues to instantiate the event handler CFC or retrieve it from cache, and then finally executes the event handler's action method.






