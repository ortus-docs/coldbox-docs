# Around Advices

![](/full/images/eventhandler-around.jpg)

Around advices are the most powerful of all as you completely hijack the requested action with your own action that looks, smells and feels exactly as the requested action. This will allow you to run both **before** and **after** advices but also **surround** the method call with whatever logic you want like `transactions`, `try/catch` blocks, `locks` or even decide to NOT execute the action at all. You can do it globally by using the `aroundHandler()` method or targeted to a specific action `around{actionName}()`.

**Examples**

```javascript
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

* `event` : The request context reference
* `targetAction` : The UDF pointer to the action that got the around interception. It will be your job to execute it \(Look at samples\)
* `eventArguments` : The struct of extra arguments sent to an action if any
* `rc` : The RC reference
* `prc` : The PRC Reference

## Exceptions & Only Lists

You can fine tune these interception methods by leveraging two public properties in the handler:

* `this.aroundhandler_only` : A list of actions that the `aroundHandler()` action will fire ONLY!
* `this.aroundhandler_except` : A list of actions that the `aroundHandler()` action will NOT fire on

```javascript
// only fire for the actions: save(), delete()
this.aroundhandler_only = "save,delete";
// DO NOT fire for the actions: login(), doLogin(), logout()
this.aroundhandler_except = "login,doLogin,logout"
```

