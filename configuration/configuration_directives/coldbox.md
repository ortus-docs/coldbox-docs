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

## Development Settings

coldbox = {
	reinitPassword = "h1cker",
	handlersIndexAutoReload = true
};

**reinitPassword**

Protect the reinitialization of the framework URL actions. For security, if this setting is omitted, you will not be able to reinitialize the framework. Setting it to an empty string will allow you to reinitialize without a password. (?fwreinit=1) Always have a password set for public-facing site.

## Implicit Event Settings

## Extension Points

## Exception Handling

## Application Aspects 