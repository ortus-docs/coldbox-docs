# Announcing Interceptions

The last piece of the puzzle is how to announce events.  You will do so via the inherited super type method  `announceInterception()` that all your handlers,plugins and even the interceptors themselves have or via the interceptor service `process()` method.  This method accepts an incoming data struct which will be broadcasted alongside your event:

```js
<---  prepare interception structure --->
<cfset var data = structnew()>
<cfset data.timeIntercepted = now()>
<cfset data.user = event.getValue("oUser")>
<cfset data.event = event.getCurrentEvent()>

<---  Broadcast Interception --->
<cfset announceInterception('onLog', data)>

<---  Alternate Broadcast Interception Syntax --->
<cfset getController().getInterceptorService().processState(state,interceptData)>
```

As you can see from the sample below, you can alternatively announce via the interceptor service's processState() method as well. You can easily inject the interceptor service in any model object and even produce announcements from your domain objects.

* announceInterception(state, interceptData) inherited by plugins, handlers and interceptors.
* getController().getInterceptorService().processState(state,interceptData)' via the controller.

