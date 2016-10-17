# Post Advices

<img src="../images/eventhandler-prepost.jpg"/>

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

## Exceptions & Only Lists

You can fine tune these interception methods by leveraging two public properties in the handler:


* <code>this.posthandler_only</code> : A list of actions that the <code>postHandler()</code> action will fire ONLY!
* <code>this.posthandler_except</code> : A list of actions that the <code>postHandler()</code> action will NOT fire on

```js
// only fire for the actions: save(), delete()
this.posthandler_only = "save,delete";
// DO NOT fire for the actions: login(), doLogin(), logout()
this.posthandler_except = "login,doLogin,logout"
```













