## Configuration As An Interceptor

<img src="/images/eventdriven.jpg">

Another cool concept for the Configuration CFC is that it is also registered as a [ColdBox Interceptor](../../interceptors/index.md) once the application starts up automatically for you. This means that you can create interception points in this CFC that will be registered upon application startup so you can define startup procedures, listen to events, etc.

```js
function preProcess(event, interceptData, buffer){
    writeDump( 'I just hijacked your app!' );abort;
}
```

Note that the config CFC does not have the same variables mixed into it that a "normal" interceptor has.  You can still access everything you need, but will need to get it from the `controller` in the variables scope.

```js
function preRender(event, interceptData, buffer){
    controller.getWirebox().getInstance( 'loggerService' ).doSomething();
}
```