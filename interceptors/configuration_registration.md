# Configuration Registration

In the `ColdBox.cfc` configuration file, there is a structure called `interceptorSettings` with two keys:

* `throwOnInvalidStates` - This tells the interceptor service to throw an exception if the state announced for interception is not valid or does not exist. By default it does not throw an exception but ignore the announcement.
* `customInterceptionPoints` - This key is a comma delimited list of custom interception points you will be registering for execution.


```js
//Interceptor Settings
interceptorSettings = {
	throwOnInvalidStates = false,
	customInterceptionPoints = "onLogin,onWikiTranslation,onAppClose"
};
```

The `customInterceptionPoints` is what interest us.  This can be a list or an array of events your system can broadcast.  This way, whenever interceptors are registered, they will be inspected for those events.