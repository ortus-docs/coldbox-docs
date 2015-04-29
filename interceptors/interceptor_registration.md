# Interceptor Registration

You can also register CFCs as interceptors programmatically by talking to the application's Interceptor Service:

```js
interceptors = [
	{class="coldbox.system.interceptors.SES", properties={}},
	{class="interceptors.RequestTrim", properties={trimAll=true}},
	{class="coldbox.system.interceptors.Security", name="AppSecurity", properties={
		rulesSource 	= "model",
		rulesModel		= "securityRuleService@cb",
		rulesModelMethod = "getSecurityRules",
		validatorModel = "securityService@cb"}
	}}
];
```

Once you have a handle on the interceptor service you can use the following methods to register interceptors:

* `registerInterceptor()` - Register an instance or CFC path and discover all events it listens to by conventions
* `registerInterceptionPoint()` - Register an instance in a specific event only

Here are the method signatures:

```js
public any registerInterceptor([any interceptorClass], [any interceptorObject], [any<struct> interceptorProperties='[runtime expression]'], [any customPoints=''], [any interceptorName])

public any registerInterceptionPoint(any interceptorKey, any state, any oInterceptor)
```

Here are a few samples of objects that can register themselves:

```js
// register yourself to listen to all events declared
controller.getInterceptorService()
	.registerInterceptor(interceptorObject=this);

// register yourself to listen to all events declared and register new events: onError, onLogin
controller.getInterceptorService()
	.registerInterceptor(interceptorObject=this, customPoints="onError,onLogin");

// Register yourself to listen to ONLY the afterInstanceAutowire event
controller.getInterceptorService()
	.registerInterceptionPoint(interceptorKey="MyService",state="afterInstanceAutowire",oInterceptor=this);
```

