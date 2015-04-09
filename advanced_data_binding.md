# Advanced Data Binding

[WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) also sports several advanced data binding techniques:

* populateFromJSON()
* populateFromQuery()
* populateFromQueryWithPrefix()
* populateFromStruct()
* populateFromXML()


We suggest you look at the latest [API Docs ](http://apidocs.coldbox.org/cbQuickDocs/search/index) for the correct arguments and such. To use these beauties you can either go to the wirebox reference directly or use the *BeanFactory* plugin. Both accomplish the same:

```js
wirebox.getObjectPopulator().populateFromXXXX()

getPlugin("BeanFactory").populateFromXXXX()
```