# Pre Advices

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

## Exceptions & Only Lists

You can fine tune these interception methods by leveraging two public properties in the handler:


* <code>this.prehandler_only</code> : A list of actions that the <code>preHandler()</code> action will fire ONLY!
* <code>this.prehandler_except</code> : A list of actions that the <code>preHandler()</code> action will NOT fire on

```js
// only fire for the actions: save(), delete()
this.prehandler_only = "save,delete";
// DO NOT fire for the actions: login(), doLogin(), logout()
this.prehandler_except = "login,doLogin,logout"
```
