# Event Handlers

Event handlers are our **controllers** in the MVC design pattern. So every time you hear event handler, you are taking about a controller that can listen to external events or internal events.

<img src="../images/ControllerLayer.jpg">

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


