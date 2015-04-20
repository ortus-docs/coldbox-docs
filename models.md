# Models

##Introduction

 Model integration helps you create, manage, and use model (business logic) objects very easily within your ColdBox application via [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm). WireBox, is our dependency injection and AOP framework, that will do all the magic of building, wiring objects with dependencies and helping your persist objects in some state (singletons, transients, request, etc). The main purpose for model integration is to make developer's development workflow easier! And we all like that Easy button! 
 
  This integration will give you a good kick start on dependency injection, caching, persistence, etc without you actually studying for it. Some very simple conventions are all you need to get you started. Now, what does model integration do for you: 
  
  * Easily create and retrieve model objects by using one method: getModel()
  * Easily handle model or handler dependencies by using cfproperty and constructor argument conventions.
  * A conventions DSL (Domain Specific Language) has been created to facilitate what needs to be injected in the models (Don't shiver with fear yet, please keep reading)
  * Easily determine what scope model objects should be persisted in: Transients, Singletons, Cache, Request, Session, etc.
  * Easily create a configuration binder to create aliases or complex object relationships (Java, WebServices, RSS, etc.)
  * Easily populate model objects or even ORM entity objects with data from a request: populateModel()
  * Easily compose ORM relationships from request data using populateModel()
  * Easily [validate](http://wiki.coldbox.org/wiki/Validation.cfm) model objects using our awesome [ValidBox](http://wiki.coldbox.org/wiki/Validation.cfm) validation engine and validateModel()
  * Integrate with ColdFusion ORM via our [ActiveEntity](http://wiki.coldbox.org/wiki/ORM:ActiveEntity.cfm) class if you like Active Record pattern or
  * Integrate with ColdFusion ORM via our [ORM Service](http://wiki.coldbox.org/wiki/ORM:BaseORMService.cfm) or [Virtual Entity Services](http://wiki.coldbox.org/wiki/ORM:VirtualEntityService.cfm).

Wow! You can do all that? Yes and much more. So let's begin.
