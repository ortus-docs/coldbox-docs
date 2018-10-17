# Locations

By convention every ColdBox application will have two folders for modules:

* **/modules** : Used by CommandBox to place tracked modules. You would usually NOT commit these modules to source control.
* **/modules\_app** : Used for custom non-tracked modules

## External Locations

You can also have more external locations that ColdBox will scan for modules by leveraging the `coldbox.modulesExternalLocation` setting. This setting is an array of locations you want to tell ColdBox to look for modules in your `ColdBox.cfc`. Each array element is the instantiation location which can use ColdFusion mappings or an absolute reference from the root of your application.

> **Caution** Internally each of those entries will be expanded for you, so please be aware of this.

```javascript
coldbox = {

 ...

 modulesExternalLocation = [ "/shared/modules", "/codedepot/modules/customer1" ]

};
```

## Duplicate Names

You might be asking yourself, what happens if there is another module in the external locations with the same name as in another location? Or even in the conventions? Well, the first one found takes precedence. So if we have a module called `funky` in our conventions folder and also in our external locations, only the one in the conventions will load as it is discovered first.

