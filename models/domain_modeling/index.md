# Domain Modeling

The Model layer represents your data structures and business logic. The domain-specific representation of the information that the application operates on. Many applications also use a persistent storage mechanism (such as a database) to store and retrieve data. MVC does not specifically mention the data access layer because it is understood to be underneath or encapsulated by the Model Layer. This is the most important part of your application and it is usually modeled by ColdFusion components, CFCs. You can even create the entire model layer in another language or physical location (Java, web services). 

All you need to understand is that this layer is the layer that runs the logic show! For the following example, I highly encourage you to also do [UML modeling](http://en.wikipedia.org/wiki/Unified_Modeling_Language), so you can visualize class relationships and design.

## UML Resources

There are tons of great UML resources and tools. Here are some great tools for you:

* Sparx Systems Enterprise Architect - http://www.sparxsystems.com/products/ea/index.html
* ColdFusion generation templates for Enterprise Architect - https://github.com/lmajano/EA-ColdFusion-CodeGeneration
* ArgoUML - http://argouml.tigris.org/
* Poseidon UML - http://www.gentleware.com/
* Learning UML - http://shop.oreilly.com/product/9780596009823.do
* UML in a nutshell - http://shop.oreilly.com/product/9780596007959.do?green=87D3081D-5A0D-50F7-9757-95B7E8779516&cmp=af-mybuy-9780596007959.IP

## Book Catalog

Let's say that I want to build a simple book catalog and I want to be able to do the following:

* List how many books I have
* Search for a book by name
* Add Books
* Remove Books
* Update Books 

There are several ways I can go about this and your design will depend on the tools you use. If you use an ORM like ColdFusion ORM you most likely will use either an [ActiveEntity](http://wiki.coldbox.org/wiki/ORM:ActiveEntity.cfm) approach or build [virtual service layers](http://wiki.coldbox.org/wiki/ORM:VirtualEntityService.cfm) or build a service layer based on our [Base ORM services](http://wiki.coldbox.org/wiki/ORM:BaseORMService.cfm). If you are not using an ORM, then most likely you will have to build all object, service and data layers manually. We will concentrate first on the last approach in order to do our modeling and then build them out in different ways.

## ORM Extensions

Since some of the examples in this section require the usage of ColdFusion ORM and the ColdBox ORM Extensions module (https://github.com/ColdBox/cbox-cborm), let's use CommandBox to install them in our application in two easy steps:

**1. Install Module**
```
install cborm
```
**2. Add Mapping**<br>
Open your `Application.cfc` and add a mapping to this module:

```js
this.mappings[ "/cborm" ] 	= COLDBOX_APP_ROOT_PATH & "modules/coldbox-bundle/cborm";
```

The manual addition of the mapping is needed since the ColdFusion ORM loads before anything in our system, so we must tell it beforehand.  We cannot use the ColdBox Module CFMappings unfortunately.

