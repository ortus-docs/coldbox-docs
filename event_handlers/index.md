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
Events are determined via a special variable that can be sent in via the FORM or URL or REMOTELY called `event`.  If no event is detected as an incoming variable, the framework will look in the configuration directives for the `DefaultEvent` and use that instead (Also set in your configuration file). If you did not set a DefaultEvent setting then the framework will use the following convention for you:

> **Hint** : You can even change the `event` variable name by updating the `EventName` setting in your `coldbox` configuration directive.






