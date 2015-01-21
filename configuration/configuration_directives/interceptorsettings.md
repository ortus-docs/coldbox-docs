# InterceptorSettings

This structures configures the interceptor service in your application.

```js
//Interceptor Settings
interceptorSettings = {
	throwOnInvalidStates = false,
	customInterceptionPoints = "onLogin,onWikiTranslation,onAppClose"
};
```

**throwOnInvalidStates**

This tells the interceptor service to throw an exception if the state announced for interception is not valid or does not exist. Defaults to **false**.

**customInterceptionPoints**

This key is a comma delimited list of custom interception points you will be registering for execution. This is the way to provide an observer-observable pattern to your applications. Again, please see the [Interceptors | Interceptor's Guide] for more information. Just note that here is where you register the custom interception points separated by a comma if more than one. This is needed so when interceptors are registered for execution points, these points will also be searched and registered for.







