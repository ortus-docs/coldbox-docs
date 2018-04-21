# Environments

The configuration CFC has embedded environment control and detection built-in. In this structure you will set up the application environments and their associated regular expressions for its `cgi` host names to match for you automatically. If the framework matches the regex with the associated `cgi.http_host`, it will set a setting called `Environment` in your configuration settings and look for that environment setting name in your CFC as a method by convention. That's right, it will check if your CFC has a method with the same name as the environment and if it exists, it will call it for you. Here is where you basically override, remove, or add any settings according to your environment.

> **Warning** : The environment detection occurs AFTER the `configure()` method is called. Therefore, whatever settings or configurations you have on the `configure()` method will be stored first, treat those as **Production** settings.

```javascript
environments = {
    // The key is the name of the environment
    // The value is a list of regex to match against cgi.http_host
    development = "^cf2016.,^lucee.,localhost",
    staging = "^stg"
};
```

In the above example, I declare a **development** key with a value list of regular expressions. If I am in a host that starts with **cf2016**, this will match and set the environment setting equal to **development**. It will then look for a development method in this CFC and execute it.

```javascript
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

## Custom Environment Detection

If you want your own detection algorithm instead of looking at the `cgi.http_host` variable, then fear not. You will NOT fill out an environments structure but actually create a method with the following signature:

```javascript
string public detectEnvironment(){
}
```

This method will be executed for you at startup and it must return the name of the environment the application is on. It will then store it and execute the method if it exists.

