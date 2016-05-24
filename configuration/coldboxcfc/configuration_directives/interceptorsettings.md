# InterceptorSettings

This structures configures the interceptor service in your application.

```js
//Interceptor Settings
interceptorSettings = {
	throwOnInvalidStates = false,
	customInterceptionPoints = "onLogin,onWikiTranslation,onAppClose"
};
```

## `throwOnInvalidStates`

This tells the interceptor service to throw an exception if the state announced for interception is not valid or does not exist. Defaults to **false**.

## `customInterceptionPoints`

This key is a comma delimited list or an array of custom interception points you will be registering for custom announcements in your applicaiton. This is the way to provide an observer-observable pattern to your applications. 

> **Info** Please see the [Interceptors](../../../interceptors/index.md) section for more information.







