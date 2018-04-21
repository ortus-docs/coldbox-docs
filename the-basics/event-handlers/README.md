# Event Handlers

Event handlers are ColdBox's version of **controllers** in the MVC design pattern. So every time you hear "event handler," you are talking about a controller that can listen to external events or internal events in ColdBox.

![](../../.gitbook/assets/controllerlayer.jpg)

## CFCs

Event handlers are implemented as CFCs that are responsible for handling requests coming into the application from either a local source \(like form, URL or REST\) or a remote source \(like Flex, Air or SOAP\). These event handlers carry the task of controlling your application flow, calling business logic, preparing a display to a user and more.

> **Info:** Event Handlers are treated as **singletons** by ColdBox, so make sure you make them thread-safe and properly scoped. Persistence is controlled by the `coldbox.handlerCaching` directive

A simple event handler:

```javascript
component extends="coldbox.system.EventHandler"{
    function index( event, rc, prc ){}
}
```

> **Info** You can also remove the inheritance from the CFC \(preferred method\) and WireBox will extend the `coldbox.system.EventHandler` for you using [Virtual Inheritance](https://wirebox.ortusbooks.com/content/virtual_inheritance/).

## Locations

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/ApplicationTemplate.png)

All your handlers will go in the **handlers** folder of your application template. Also notice that you can create packages or sub-folders inside of the handlers directory. This is encouraged on large applications so you can section off or package handlers logically and get better maintenance and URL experience. If you get to the point where your application needs even more decoupling and separation, please consider building [ColdBox Modules](../../hmvc/modules/) instead.

### Event Handlers External Location

You can also declare a `HandlersExternalLocation` setting in your [Configuration CFC](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/full/event_handlers/configuration/configuration_directives/coldbox.md). This will be a dot notation path or instantiation path where more external event handlers can be found \(You can use coldfusion mappings\).

```javascript
coldbox.handlersExternalLocation  = "shared.myapp.handlers";
```

> **Note**: If an external event handler has the same name as an internal conventions event, the internal conventions event will take precedence.

## Handler Registration

At application startup, the framework registers all the valid event handler CFCs in these locations \(plus handlers inside of modules\). So for development it makes sense to activate the following setting in your [Configuration CFC](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/full/event_handlers/configuration/configuration_directives/coldbox.md):

```javascript
// Reload handlers on each request
coldbox.handlersIndexAutoReload = true;
// Deactivate singleton caching of the handlers
coldbox.handlerCaching = false;
```

## Constructors

Event handler controllers do not require a constructor because the extended class already provides one. However, if you want one, you can still create one:

**Non-inheritance**

```javascript
component{

    function init(){
        // my stuff here
        return this;
    }

}
```

> **Info** You have access to a `$super` object in this approach.

**Inheritance**

```javascript
component extends="coldbox.system.EventHandler"{

    function init( required controller ){
        // init super
        super.init( arguments.controller );
        // my stuff here
        return this;
    }

}
```

## Handler Actions

Every method in this event handler controller that has an access of `public` is automatically exposed as a runnable event in ColdBox and will be auto-registered for you. That means there is no extra configuration or XML logic to define them. By convention they become alive once you create them and clients can request them. In ColdBox terms, each of these event handler methods are referred to as **actions**. ColdBox captures an incoming variable called `event` \(from the form, URL or remote scopes\) and uses it to execute the correct event handler and action method.

```javascript
component extends="coldbox.system.EventHandler"{

    function index( event, rc, prc ){
        return "Hi from handler land!";
    }

    private function myData( event, rc, prc ){
        return ['coldbox', 'wirebox', 'cachebox', 'logbox'];
    }
}
```

So what about `private` functions? You can still use them in your application as local helpers to a handler object or can be called across handlers via a nice function called `runEvent()` that we will explore later.

### Default Action: index\(\)

The default action of ANY handlers is the method `index()`. If you try to execute an event handler without defining an action, ColdBox will execute the default action instead.

> **Danger** : Event Handlers are not to be used to write business logic. They should be light and fluffy!

### Action Arguments

```javascript
function myAction( event, rc, prc ){
    return "Hi from handler land!";
}
```

Each method action that you write receives some arguments:

* **event** : The request context object reference \(`coldbox.system.web.context.RequestContext`\)
* **rc** : A reference to the request collection inside of the request context object
* **prc** : A reference to the private request collection inside of the request context object

> **Note** The **rc** and **prc** references each method receives are sent for convenience so that you can interact with the structures instead of through the **event** object's methods. Interacting with structures over methods is much more performant.

The request context object has tons of methods to help you in setting and getting variables from one layer to another, getting request metadata, rendering RESTful content, setting HTTP headers, and more. It is your information superhighway for specific requests. Remember that the API Docs are your best friend!

