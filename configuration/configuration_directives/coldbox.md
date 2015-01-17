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

Protect the reinitialization of the framework URL actions. For security, if this setting is omitted, you will not be able to reinitialize the framework. Setting it to an empty string will allow you to reinitialize without a password. (?fwreinit=1) Always have a password set for public-facing site.

**handlersIndexAutoReload**

Will scan the conventions directory for new handler CFCs on each request if activated. Use **false** for production, this is only a development true setting.


## Implicit Event Settings

```js
coldbox={
	//Implicit Events
	defaultEvent = "Main.index",
	requestStartHandler	= "Main.onRequestStart",
	requestEndHandler = "Main.onRequestEnd",
	applicationStartHandler = "Main.onAppInit",
	applicationEndHandler = "Main.onAppEnd",
	sessionStartHandler = "Main.onSessionEnd",
	sessionEndHandler = "Main.onSessionStart",
	missingTemplateHandler = "Main.onMissingTemplate"
}
```

## Extension Points

## Exception Handling

## Application Aspects 