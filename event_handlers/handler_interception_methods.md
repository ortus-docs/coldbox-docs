# Interception Methods

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


## Pre Advices

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
}
```

The arguments received by these interceptors are:

* <code>event</code> : The request context reference
* <code>action</code> : The action name that was intercepted
* <code>eventArguments</code> : The struct of extra arguments sent to an action if any
* <code>rc</code> : The RC reference
* <code>prc</code> : The PRC Reference

### Exceptions & Only Lists

You can fine tune these interception methods by leveraging two public properties in the handler:


* <code>this.prehandler_only</code> : A list of actions that the <code>preHandler()</code> action will fire ONLY!
* <code>this.prehandler_except</code> : A list of actions that the <code>preHandler()</code> action will NOT fire on

```js
// only fire for the actions: save(), delete()
this.prehandler_only = "save,delete";
// DO NOT fire for the actions: login(), doLogin(), logout()
this.prehandler_except = "login,doLogin,logout"
```

## Post Advices

With this interceptor you can intercept local event actions and execute things after the requested action executes. You can do it globally by using the <code>postHandler()</code> method or targeted to a specific action <code>post{actionName}()</code>.

```js
// executes after any action
function postHandler(event,action,eventArguments,rc,prc){
}

// executes after the list() action ONLY
function postList(event,action,eventArguments,rc,prc){
}

// concrete examples
function postHandler(event,action,eventArguments,rc,prc){
	log.info("Finalized executing #action#");
}
```

The arguments received by these interceptors are:

* <code>event</code> : The request context reference
* <code>action</code> : The action name that was intercepted
* <code>eventArguments</code> : The struct of extra arguments sent to an action if any
* <code>rc</code> : The RC reference
* <code>prc</code> : The PRC Reference

### Exceptions & Only Lists

You can fine tune these interception methods by leveraging two public properties in the handler:


* <code>this.posthandler_only</code> : A list of actions that the <code>postHandler()</code> action will fire ONLY!
* <code>this.posthandler_except</code> : A list of actions that the <code>postHandler()</code> action will NOT fire on

```js
// only fire for the actions: save(), delete()
this.posthandler_only = "save,delete";
// DO NOT fire for the actions: login(), doLogin(), logout()
this.posthandler_except = "login,doLogin,logout"
```













