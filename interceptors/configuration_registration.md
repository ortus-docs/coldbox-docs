# Configuration Registration

In the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) file, there is a structure called interceptorSettings with two keys:

* throwOnInvalidStates - This tells the interceptor service to throw an exception if the state announced for interception is not valid or does not exist. By default it does not throw an exception but ignore the announcement.
* customInterceptionPoints - This key is a comma delimited list of custom interception points you will be registering for execution.


```js
//Interceptor Settings
interceptorSettings = {
	throwOnInvalidStates = false,
	customInterceptionPoints = "onLogin,onWikiTranslation,onAppClose"
};
```

