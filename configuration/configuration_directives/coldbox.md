# ColdBox
The ColdBox directive is where you configure the framework for operation.  

## Application Setup

```js
coldbox = {
    // The name of the application
	appName   = "My App",
	// The name of the incoming URL/FORM/REMOTE variable that tells the framework what event to execute. Ex: index.cfm?event=users.list
	eventName = "event"
};
```

> **Info** : Please note that the `appName` setting is the ONLY mandatory setting in ANY directive.

## Development Settings

```js
coldbox = {
	reinitPassword = "h1cker",
	handlersIndexAutoReload = true
};
```

**reinitPassword**

Protect the reinitialization of the framework URL actions. For security, if this setting is omitted, you will not be able to reinitialize the framework. Setting it to an empty string will allow you to reinitialize without a password. **Always have a password set for public-facing site.**

```
// reinit with no password
http://localhost/?fwreinit=1
// reinit with password
http://localhost/?fwreinit=mypass
`

**handlersIndexAutoReload**

Will scan the conventions directory for new handler CFCs on each request if activated. Use **false** for production, this is only a development true setting.


## Implicit Event Settings

```js
coldbox={
	//Implicit Events
	defaultEvent  = "Main.index",
	requestStartHandler	 = "Main.onRequestStart",
	requestEndHandler   = "Main.onRequestEnd",
	applicationStartHandler = "Main.onAppInit",
	applicationEndHandler = "Main.onAppEnd",
	sessionStartHandler = "Main.onSessionEnd",
	sessionEndHandler = "Main.onSessionStart",
	missingTemplateHandler = "Main.onMissingTemplate"
}
```
These settings map 1-1 from ColdBox events to the `Application.cfc` life-cycle methods.  The only one that is not is the `defaultEvent`, which selects what event the framework will execute when no incoming event is detected via URL/FORM or REMOTE executions.

## Extension Points

```js
coldbox={
	//Extension Points
	applicationHelper 			= "includes/helpers/ApplicationHelper.cfm",
	viewsHelper					= "",
	modulesExternalLocation		= [],
	viewsExternalLocation		= "",
	layoutsExternalLocation 	= "",
	handlersExternalLocation  	= "",
	requestContextDecorator 	= "",
	controllerDecorator         = ""
}
```

**applicationHelper**

A list or array of absolute or relative paths to a UDF helper file. The framework will load all the methods found in this helper file globally. Meaning it will be injected in ALL handlers, layouts and views.

**viewsHelper**

A list or array of absolute or relative paths to a UDF helper file. The framework will load all the methods found in this helper in layouts and views only.

**modulesExternalLocation**
A list or array of locations of where ColdBox should look for modules to load into your application. The path can be a cf mapping or `cfinclude` compatible location. Modules are searched and loaded in the order of the declared locations. The first location ColdBox will search for modules is the conventions folder `modules`

**viewsExternalLocation**

The CF include path of where to look for secondary views for your application. Secondary views look just like normal views except the framework looks in the conventions folder first and if not found then searches this location.

**layoutsExternalLocation**

The CF include path of where to look for secondary layouts for your application. Secondary layouts look just like normal layouts except the framework looks in the conventions folder first and if not found then searches this location.

**handlersExternalLocation**
The CF dot notation path of where to look for secondary events for your application. Secondary events look just like normal events except the framework looks in the conventions folder first and if not found then searches this location.




## Exception Handling

## Application Aspects 