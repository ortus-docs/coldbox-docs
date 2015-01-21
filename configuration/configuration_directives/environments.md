# Environments

The configuration CFC has embedded environment control and detection built-in. In this structure you will setup the application environments and their associated regular expressions for its `cgi` host names to match for you automatically. If the framework matches the regex with the associated `cgi.http_host` then it will set a setting called `Environment` in your configuration settings and will then go ahead and look for that environment setting name in your CFC as a method by convention. That's right, it will check if your CFC has a method with the same name as the environment and if it exists it will call it for you. Here is where you basically override, remove or add any settings according to your environment.

> **Warning** : The environment detection occurs AFTER the `configure()` method is called. Therefore, whatever settings or configurations you have on the `configure()` method will be stored first, treat those as **Production** settings.

```js
environments = {
    // The key is the name of the environment
    // The value is a list of regex to match against cgi.http_host
	development = "^cf9.,^railo.,localhost",
	staging = "^stg"
};
```

In the above example, I declare a **development** key with a value list of a regular expressions. So if I am in a host that starts with **cf9** this will match and set the environment setting equal to **development** and then look for a development method in this CFC and execute it:

```js
/**
* Executed whenever the development environment is detected
*/
function development(){
	// Override coldbox directives
	coldbox.handlerCaching = false;
	coldbox.eventCaching = false;
	coldbox.debugPassword = "";
	coldbox.reinitPassword = "";
	
	// Add dev only interceptors
	arrayAppend( interceptors, {class="#appMapping#.interceptors.CustomLogger} );
}
```





