# Locations

By convention every ColdBox application will have two folders for modules:

* **/modules** : Used by CommandBox to place tracked modules
* **/modules_app** : Used for custom non-tracked modules

A typical ColdBox application will have a directory called `modules`, if not just create it. Here is where you will place all your ColdBox modules in and giving each one of them a unique folder name. However, you can also tell ColdBox to not only look in the conventions folder for your modules but anywhere in the server you like with the ColdBox configuration directive: `coldbox.modulesExternalLocation`. This setting is an array of locations you want to tell ColdBox to look for modules in your `ColdBox.cfc`. Each array element is the instantiation location which can use ColdFusion mappings or an absolute reference from the root of your application.

## External Locations

You can also have more external locations that ColdBox will scan for modules by leveraging the `coldbox.modulesExternalLocation` setting.

> **Caution** Internally each of those entries will be expanded for you, so please be aware of this. 

```js
coldbox = {
  .. all settings above
  modulesExternalLocation = ["/shared/modules", "/codedepot/modules/customer1"]
};
```

In the example above, ColdBox will first register all the modules in the local application conventions folder, `modules`, and then look in order of declaration of the external locations. You might be asking yourself, what happens if there is another module in the external location with the same name as in another location? Or even in the conventions? Well, the first one found takes precedence. So if we have a module called funky in our conventions folder and also in our external locations, only the one in the conventions will load as it is discovered first.

