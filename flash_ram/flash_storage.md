# Flash Storage

There are times where you need to store user related variables in some kind of permanent storage then relocate the user into another section of your application, be able to retrieve the data, use it and then clean it. All of these tedious operations are definitely doable by why reinvent the wheel if we can have the platform give us a tool for maintaing conversation variables across requests. The key point for Flash RAM is where will the data be stored so that it is unique per user. ColdFusion gives us several persistent scopes that we can use and we have also created several flash storages for this purpose. Since the ColdBox flash scope is based on an interface, the flash scope storage can be virtually anywhere. You will find all of these implementations in the following package: coldbox.system.web.flash. In order to choose what implementation to use in your application you need to tell the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) which one to use via flash configuration structure:

### Configuration

```js
// flash scope configuration
flash = {
	scope = "session,client,cluster,cache,or full path",
	properties = {}, // constructor properties for the flash scope implementation
	inflateToRC = true, // automatically inflate flash data into the RC scope
	inflateToPRC = false, // automatically inflate flash data into the PRC scope
	autoPurge = true, // automatically purge flash data for you
	autoSave = true // automatically save flash scopes at end of a request and on relocations.
};
```

Below is a nice chart of all the keys in this configuration structure so you can alter behavior of the Flash RAM objects:

|Key|Type|Required|Default|Description|
|--|--|--|--|--|
|scope|string or instantiation path |false|*session*|Determines what scope to use for Flash RAM. The available aliases are: session, client, cluster, cache or a custom instantiation path|
|properties|struct|false|{}|Properties that can be used inside the constructor of any Flash RAM implementation|
|inflateToRC |boolean|false|true|Whatever variables you put into the Flash RAM, they will also be inflated or copied into the request collection for you automatically.|
|inflateToPRC |boolean|false|false|Whatever variables you put into the Flash RAM, they will also be inflated or copied into the private request collection for you automatically|
|autoPurge |boolean|false|true|This is what makes the Flash RAM work, it cleans itself for you. Be careful when setting this to false as it then becomes your job to do the cleaning|
|autoSave |boolean|false|true|The Flash RAM saves itself at the end of requests and on relocations via setNextEvent(). If you do not want auto-saving, then turn it off and make sure you save manually|