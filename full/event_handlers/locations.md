# Locations

<img src="https://coldbox.ortusbooks.com/content/images/ApplicationTemplate.png">

All your handlers will go in the **handlers** folder of your application template. Also notice that you can create packages or sub-folders inside of the handlers directory. This is encouraged on large applications so you can section off or package handlers logically and get better maintenance and URL experience. If you get to the point where your application needs even more decoupling and separation, please consider building [ColdBox Modules](../modules/index.md) instead.

## Event Handlers External Location
You can also declare a `HandlersExternalLocation` setting in your [Configuration CFC](configuration/configuration_directives/coldbox.md). This will be a dot notation path or instantiation path where more external event handlers can be found (You can use coldfusion mappings).

```js
coldbox.handlersExternalLocation  = "shared.myapp.handlers";
```

> **Note**: If an external event handler has the same name as an internal conventions event, the internal conventions event will take precedence.

##Handler Registration

At application startup, the framework registers all the valid event handler CFCs in these locations (plus handlers inside of modules). So for development it makes sense to activate the following setting in your [Configuration CFC](configuration/configuration_directives/coldbox.md):

```js
// Reload handlers on each request
coldbox.handlersIndexAutoReload = true;
// Deactivate singleton caching of the handlers
coldbox.handlerCaching = false;
```