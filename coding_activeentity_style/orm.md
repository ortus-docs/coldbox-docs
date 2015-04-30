# ORM

You will first make sure your `contacts` datsource exists in the Administrator and then we can declare our ORM settings in our `Application.cfc`

```js
// ORM Settings
this.ormEnabled 	  = true;
this.datasource		  = "contacts";
this.ormSettings	  = {
	cfclocation = "models",
	dbcreate	= "update",
	logSQL 		= true,
	flushAtRequestEnd = false,
	autoManageSession = false,
	eventHandling 	  =  true,
	eventHandler	  = "cborm.models.EventHandler"
};

ORMReload();
```

These are the vanilla settings for using the ORM with ColdBox. Make sure that flustAtRequestEnd and autoManageSession are set to false as ColdBox will manage that for you. In this example, we also use dbcreate="update" as we want ColdFusion ORM to build the database for us which allows us to concentrate on the domain problem at hand and not persistence. Finally, add an ormReload() at the end for now as we are in development mode and want ORM changes to take effect immediately. For production, remove it. You also see that we add our own eventHandler which points to the vanilla [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) event handler so we can make Active Entity become well, Active!

Now open your ColdBox.cfc and add the following to activate ORM injections:

```js
orm = { injection = {enabled=true} };
```

