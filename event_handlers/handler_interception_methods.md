# Handler Interception Methods

<img src="../images/eventhandler-prepost.jpg"/>

There are also several simple implicit [AOP](http://en.wikipedia.org/wiki/Aspect-oriented_programming) interceptors, usually referred as advices, that can be declared in your event handler that the framework will use in order to execute them anytime an event is fired from the current handler. This is great for intercepting calls, pre/post processing, localized security, logging, RESTful conventions and much more. Yes, you got that right, Aspect Oriented Programming just for you and without all the complicated setup involved! If you declared them, the framework will execute them.

| Interceptor Method | Description |
| -- | -- |
| preHandler | Executes before any requested action (In the same handler CFC)  |
| pre{action} | Executes before the {action} requested ONLY |
| postHandler | Executes after any requested action (In the same handler CFC)  |
| post{action} | Executes after the {action} requested ONLY |
| aroundHandler | Executes around any request action (In the same handler CFC)
| around{action} | Executes around the {action} requested ONLY


## Pre Advice

With this interceptor you can intercept local event actions and execute things before the requested action executes. You can do it globally by using the <code>preHandler()</code> method or targeted to a specific action <code>pre{actionName}()</code>.

```js
// executes before any action
function preHandler(event,action,eventArguments,rc,prc){
}

// executes before the list() action ONLY
function preList(event,action,eventArguments,rc,prc){
}

// concrete example
function preHandler(event,action,eventArguments,rc,prc){
	if( !security.isLoggedIn() ){
		event.overrideEvent('security.login');
		log.info("Unauthorized accessed detected!", getHTTPRequestData());
	}
}
function preList(event,action,eventArguments,rc,prc){
	log.info("Starting executing the list action");
	getPlugin("Timer").start('list-profile');
}
```

The arguments received by these interceptors are:












