# My First Custom Exception Handler

### How to enable

In order to use a custom exception handler you will have to declare it first in your application's [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm):

```js
coldbox.exceptionHandler = "main.onException";
```

This setting tells ColdBox to look for the main event handler and execute the onException method on it anytime there is an unhandled exception in a framework request. It is important to note that this handler only traps framework requests, not other requests to other non-framework templates. That's it, this is all you need to do to point all exceptions to one event.

### The exception handler method

Below you can see a sample custom exception handler method that exists in my main handler. Remember that it is your job to log the errors now, since you told ColdBox that you are going to handle errors. Once an exception is detected by the framework, it will take a snapshot of the error alongside some current state information and create an ExceptionBean object for you. It will then place this object on the request collection for your method to pick it up and do something with it. The exception bean object models a ColdFusion exception structure and you can find all the methods in this object by visiting the Live API. Below is a simple example:

```js
  function onException(event,rc,prc){

  switch ( rc.exceptionBean.getType() ){
    case "Framework.plugins.settings.EventHandlerNotRegisteredException":
      getPlugin("MessageBox").setMessage("warning","This page does not exist");
      setNextEvent("forums.índex");
      break;
				
    default:
      //Manually log the errors using the logger plugin.
      getPlugin("Logger").logErrorWithBean(rc.ExceptionBean);
     
  }
  //What else, in this case, nothing, the framework will show the exception template
  </cfscript>
</cffunction>
```

As you can see from the example above, this event handler method grabs the exception bean from the request collection and can manipulate and use it in any way you like. In this example, I just have a simple if statement that checks for what type of exception occurred. Then I will use the messagebox plugin to set a warning message and relocate to my home event. No error is logged automatically by ColdBox since you are using the custom exception handler. Therefore, you must do EVERYTHING!! So the next line of code is the actual logging of the error.

```js
// Log the error.
getPlugin("Logger").logErrorWithBean(rc.ExceptionBean);
```

After this line of code, the event handler method finalizes execution. The framework then takes control again and presents the default error page or if the Custom Error Template has been declared in your configuration file, it will render that template.

### Conclusion

As you can see from this example, creating an exception handler is very very easy. The difficult part is on what to do with the exceptions and that is up to you!! You have complete control on flow and error renderings, have fun!

