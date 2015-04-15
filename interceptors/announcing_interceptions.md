# Announcing Interceptions

That is awesome, I declared them and then I code them, but how do they get executed? Well, there is a special method called announceInterception() that all your handlers,plugins and even the interceptors themselves receive via the framework super type. You can call this method with some data and all events listening will be dispatched:

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

