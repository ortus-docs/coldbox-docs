# Models

ColdBox allows you to integrate with the model layer with easy by leveraging **WireBox** as your default dependency injection framework.  However, you can integrate your model layer with any third-party DI framework via our `cbioc` module (https://github.com/ColdBox/cbox-ioc).

![](../images/WireBox.png)

WireBox, is our dependency injection and AOP framework, that will do all the magic of building, wiring objects with dependencies and helping your persist objects in some state (singletons, transients, request, etc). The main purpose for model integration is to make developer's development workflow easier! And we all like that Easy button! 

## Benefits 

* Easily create and retrieve model objects by using one method: `getModel()`
* Easily handle model or handler dependencies by using `cfproperty` and constructor argument conventions.
* An Injection DSL (Domain Specific Language) has been created to facilitate dependencies
* Easily determine what scope model objects should be persisted in: Transients, Singletons, Cache, Request, Session, etc.
* Easily create a configuration binder to create aliases or complex object relationships (Java, WebServices, RSS, etc.)
* Easily do FORM data binding or advanced ORM data binding
* Easily compose ORM relationships from request data

Wow! You can do all that? Yes and much more. So let's begin.
