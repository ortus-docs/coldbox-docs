# Pre Advices

![](../../../.gitbook/assets/eventhandler-prepost%20%282%29%20%281%29.jpg)

With this interceptor you can intercept local event actions and execute things **before** the requested action executes. You can do it globally by using the `preHandler()` method or targeted to a specific action `pre{actionName}()`.

```javascript
// executes before any action
function preHandler( event, action, eventArguments, rc, prc ){
}

// executes before the list() action ONLY
function preList( event, action, eventArguments, rc, prc ){
}

// concrete example
function preHandler( event, action, eventArguments, rc, prc ){
    if( !security.isLoggedIn() ){
        event.overrideEvent( 'security.login' );
        log.info( "Unauthorized accessed detected!", getHTTPRequestData() );
    }
}
function preList event, action, eventArguments, rc, prc ){
    log.info("Starting executing the list action");
}
```

The arguments received by these interceptors are:

* `event` : The request context reference
* `action` : The action name that was intercepted
* `eventArguments` : The struct of extra arguments sent to an action if executed via `runEvent()`
* `rc` : The **RC** reference
* `prc` : The **PRC** Reference

## Exceptions & Only Lists

You can fine tune these interception methods by leveraging two public properties in the handler:

* `this.prehandler_only` : A list of actions that the `preHandler()` action will fire ONLY!
* `this.prehandler_except` : A list of actions that the `preHandler()` action will NOT fire on

```javascript
// only fire for the actions: save(), delete()
this.prehandler_only = "save,delete";
// DO NOT fire for the actions: login(), doLogin(), logout()
this.prehandler_except = "login,doLogin,logout"
```

