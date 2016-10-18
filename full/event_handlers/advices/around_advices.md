# Around Advices


<img src=".././images/eventhandler-around.jpg"/>


Around advices are the most powerful of all as you completely hijack the requested action with your own action that looks, smells and feels exactly as the requested action. This will allow you to run both **before** and **after** advices but also **surround** the method call with whatever logic you want like <code>transactions</code>, <code>try/catch</code> blocks, <code>locks</code> or even decide to NOT execute the action at all. You can do it globally by using the <code>aroundHandler()</code> method or targeted to a specific action <code>around{actionName}()</code>.

**Examples**
```js
// executes around any action
function aroundHandler(event,targetAction,eventArguments,rc,prc){
}

// executes around the list() action ONLY
function aroundList(event,targetAction,eventArguments,rc,prc){
}

// Around handler advice for transactions
function aroundHandler(event,targetAction,eventArguments,rc,prc){

	// log the call
	log.debug("Starting to execute #targetAction.toString()#" );

	// start a transaction
	transaction{
	
		// prepare arguments for action call
		var args = {
			event = arguments.event,
			rc    = arguments.rc,
			prc   = arguments.prc

		};
		structAppend( args, eventArguments );
		// execute the action now
		var results = arguments.targetAction( argumentCollection=args );
	}
	
	// log the call
	log.debug( "Ended executing #targetAction.toString()#" );
	
	// return if it exists
	if( !isNull( results ) ){ return results; }
}

// Around handler advice for try/catches
function aroundHandler(event,targetAction,eventArguments,rc,prc){

	// log the call
	if( log.canDebug() ){
		log.debug( "Starting to execute #targetAction.toString()#" );
	}

	// try block
	try{
	
		// prepare arguments for action call
		var args = {
			event = arguments.event,
			rc    = arguments.rc,
			prc   = arguments.prc

		};
		structAppend( args, eventArguments );
		// execute the action now
		return arguments.targetAction( argumentCollection=args );
	}
	catch(Any e){
		// log it
		log.error("Error executing #targetAction.toString()#: #e.message# #e.detail#", e);
		// set exception in request collection and set view to render
		event.setValue( "exception", e)
			.setView( "errors/generic" );
	
	}

}
```

The arguments received by these interceptors are:

* <code>event</code> : The request context reference
* <code>targetAction</code> : The UDF pointer to the action that got the around interception. It will be your job to execute it (Look at samples)
* <code>eventArguments</code> : The struct of extra arguments sent to an action if any
* <code>rc</code> : The RC reference
* <code>prc</code> : The PRC Reference


## Exceptions & Only Lists

You can fine tune these interception methods by leveraging two public properties in the handler:


* <code>this.aroundhandler_only</code> : A list of actions that the <code>aroundHandler()</code> action will fire ONLY!
* <code>this.aroundhandler_except</code> : A list of actions that the <code>aroundHandler()</code> action will NOT fire on

```js
// only fire for the actions: save(), delete()
this.aroundhandler_only = "save,delete";
// DO NOT fire for the actions: login(), doLogin(), logout()
this.aroundhandler_except = "login,doLogin,logout"
```




