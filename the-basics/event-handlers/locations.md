# Locations

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/ApplicationTemplate.png)

All your handlers will go in the **handlers** folder of your application template. Also notice that you can create packages or sub-folders inside of the handlers directory. This is encouraged on large applications so you can section off or package handlers logically and get better maintenance and URL experience. If you get to the point where your application needs even more decoupling and separation, please consider building [ColdBox Modules](../../hmvc/modules/) instead.

## Event Handlers External Location

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

