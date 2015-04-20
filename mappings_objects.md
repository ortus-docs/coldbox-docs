# Mappings Objects

You can use the [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) configuration binder to map object relationships, aliases, etc. By convention you retrieve objects that exist in the model folder with their appropriate name and path. This is great and dandy, but sometimes if we need to refactor our paths, most likely our code will have to change. If you want to prepare for the future and be extensible, the best approach is to create aliases for your objects so you retrieve them by alias instead of path locations. You can do this using our [mapping DSL](http://wiki.coldbox.org/wiki/WireBox.cfm#Mapping_DSL).

```js
// Create an alias of MyService for the full instantiation path
map("MyService").to("#appmapping#.model.users.MyService");

// Map the entire model directory and the alias is the name of the CFC
mapDirectory("#appMapping#.model");
```

> **Important**: The variable appMapping is the location of your application in the web root. Always use it so your application is portable. 