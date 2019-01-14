# ORM

You will first make sure your `contacts` datsource exists in the Administrator and then we can declare our ORM settings in our `Application.cfc`

```javascript
// ORM Settings
this.ormEnabled       = true;
this.datasource          = "contacts";
this.ormSettings      = {
    cfclocation = "models",
    dbcreate    = "update",
    logSQL         = true,
    flushAtRequestEnd = false,
    autoManageSession = false,
    eventHandling       =  true,
    eventHandler      = "cborm.models.EventHandler"
};
```

These are the vanilla settings for using the ORM with ColdBox. Make sure that `flushAtRequestEnd` and `autoManageSession` are set to **false** as the ORM extensions will manage that for you.

In this example, we also use `dbcreate="update"` as we want ColdFusion ORM to build the database for us which allows us to concentrate on the domain problem at hand and not persistence. You also see that we add our own eventHandler which points to the extension's event handler so we can make Active Entity become well, Active!

## Activating ORM injections

Now open your `ColdBox.cfc` and add the following to activate ORM injections inside of your `configure()` method.

```javascript
orm = { injection = { enabled=true } };
```

