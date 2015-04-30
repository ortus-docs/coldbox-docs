# Injeection DSL

Before we start building our objects we need to know how to tell WireBox to inject objects for us. You can do all this via configuration (Inside the binder) like any other DI framework, but the easieast approach is to use our injection annotation and conventions. The injection DSL can be applied to `cfproperty, cfargument, cffunction` or called via `getModel()` as we saw previously. 

The injection DSL is represented by adding an attribute called `inject` to properties and arguments. This attribute is like your code shouting, **Hey, I need something here, hello, I need something!**. That something can be another object, setting, cache, etc.

> **Info** If you do not like annotations or find them obtrusive then you don't have to use them :).  Just leverage the WireBox binder to define object dependencies instead.

```js
// property injection
property name="service" inject="MyService";

// setter injection
function setMyservice(MyService) inject="MyService"{
	variables.MyService = arguments.MyService;
}

// argument injection
<cfargument name="setting" inject="coldbox:setting:MyDSN">

/**
* @myDAO.inject model:MyDAO
*/
function init(myDAO){
	
}
```

The value of the inject attribute is what we call our injection DSL. This string represents the concept of retrieving an object WireBox knows about, which can be an object, a setting, a datasource, your custom data, etc. You can see al the different Injection DSL's in the WireBox documentation: ([See Injection DSL](http://wiki.coldbox.org/wiki/WireBox.cfm#Injection_DSL)). Below are just the most common ones we will use:

