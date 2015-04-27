# Using Flash RAM

There are several ways to interact with the ColdBox Flash RAM:

* Using the `flash` scope object (Best Practice)
* Using the `persistVariables()` method from the super type and ColdBox Controller
* Using the `persistence` arguments in the `setNextEvent()` method from the super type and ColdBox Controller.

All of these methods interact with the Flash RAM object but the last two methods not only place variables in the temporary storage bin but actualy serialize the data into the Flash RAM storage immediately. The first approach queues up the variables for serialization and at the end of a request it serializes the variables into the correct storage scope, thus saving precious serialization time. In the next section we will learn what all of this means.

### Flash Scope Object

The flash scope object is our best practice approach as it clearly demarcates the code that the developer is using the flash scope for persistence. Any flash scope must inherit from our AbstractFlashScope and has access to several key methods that we will cover in this section. However, let's start with how the flash scope stores data:

1. The flash persistence methods are called for saving data, the data is stored in an internal temporary request bin and awaiting serialization and persistence either through relocation or termination of the request.
2. If the flash methods are called with immediate save arguments, then the data is immediately serialized and stored in the implementation's persistent storage.
3. If the flash's saveFlash() method is called then the data is immediately serialized and stored in the implementation's persistent storage.
4. If the application relocates via setNextEvent() or a request finalizes then if there is data in the request bin, it will be serialized and stored in the implementation's storage.

> **Important** By default the Flash RAM queues up serializations for better performance, but you can alter the behavior programmatically or via the configuration file.

> **Info** If you use the persistVariables() method or any of the persistence arguments on the setNextEvent() method, those variables will be saved and persisted immediately.


To review the Flash Scope methods, please [go to the API](http://apidocs.coldbox.org/) and look for the correct implementation or the AbstractFlashScope. Please note that the majority of a Flash scope methods return itself so you can concatenate method calls. Below are the main methods that you can use to interact with the Flash RAM object:


###### clear()

Clears the temporary storage bin

```js
flash.clear();
```

###### clearFlash()
Clears the persistence flash storage implementation
```js
flash.clearFlash();
```

###### discard()
```js
any discard([string keys=''])
```
Discards all or some keys from flash storage. You can use this method to implement flows.

```js
// discard all flash variables
flash.discard();

// dicard some keys
flash.discard('userID,userKey,cardID');
```
###### exists()
```js
boolean exists(string name)
```
Checks if a key exists in the flash storage
```js
if( flash.exists('notice') ){
 // do something
}
```
###### get()
```js
any get(string name, [any default])
```

Get's a value from flash storage and you can even pass a default value if it does not exist.

```js
// Get a flash key that you know exists
cardID = flash.get("cardID");

// Render some flash content
<div class="notice">#flash.get("notice","")#</div>
```

###### getKey()
Get a list of all the objects in the temp flash scope.
```js
Flash Keys: #structKeyList( flash.getKeys() )#
```

###### getFlash()
Get a reference to the permanent storage implementation of the flash scope.
```js
<cfdump var="#flash.getFlash()#">
```

###### getScope()
Get the flash temp request storage used throughout a request until flashed at the end of a request.
```js
<cfdump var="#flash.getScope()#">
```
###### isEmpty()
Check if the flash scope is empty or not
```js
<cfif !flash.isEmpty()>
</cfif>
```

###### keep()
```js
any keep([string keys=''])
```
Keep all or a single flash temp variable alive for another relocation. Usually called from interceptors or event handlers to create conversations and flows of data from event to event.

```js
function step2(event){
	// keep variables for step 2 wizard
	flash.keep('userID,fname,lname');

	// keep al variables
	flash.keep();
}
```

###### persistRC()
```js
any persistRC([string include=''], [string exclude=''], [boolean saveNow='false'])
```

Persist keys from the ColdBox request collection into flash scope. If using exclude, then it will try to persist the entire request collection but excluding certain keys. Including will only include the keys passed from the request collection.

```js
// persist some variables that can be reinflated into the RC upon relocation
flash.persistRC(include="name,email,address");
setNextEvent('wizard.step2');

// persist all RC variables using exclusions that can be reinflated into the RC upon relocation
flash.persistRC(exclude="ouser");
setNextEvent('wizard.step2');

// persist some variables that can be reinflated into the RC upon relocation and serialize immediately
flash.persistRC(include="email,addressData",savenow=true);
```

###### put()
```js
any put(string name, any value, [boolean saveNow='false'], [boolean keep='true'], [boolean inflateToRC=FROMConfig], [boolean inflateToPRC=FROMConfig], [boolean autoPurge=FROMConfig)
```

This is the main method to place data into the flash scope. You can optionally use the arguments to save the flash immediately, inflate to RC or PRC on the next request and if the data should be auto purged for you. You can also use the configuration settings to have a consistent flash experience, but you can most certainly override the defaults. By default all variables placed in flash RAM are automatically purged in the next request once they are inflated UNLESS you use the keep() methods in order to persist them longer or create flows. However, you can also use the autoPurge argument and set it to false so you can control when the variables will be removed from flash RAM. Basically a glorified ColdFusion scope that you can use.

```js
// persist some variables that can be reinflated into the RC upon relocation
flash.put(name="userData",value=userData);
setNextEvent('wizard.step2');

// put and serialize immediately.
flash.put(name="userData",value=userData,saveNow=true);

// put and mark them to be reinflated into the PRC only
flash.put(name="userData",value=userData,inflateToRC=false,inflateToPRC=true);


// put and disable autoPurge, I will remove it when I want to!
flash.put(name="userData",value=userWizard,autoPurge=false);
```

###### putAll()
```js
any putAll(struct map, [boolean saveNow='false'], [boolean keep='true'], [boolean inflateToRC='[runtime expression]'], [boolean inflateToPRC='[runtime expression]'], [boolean autoPurge='[runtime expression]'])
```

Same as the put() method but instead you pass in an entire structure of name-value pairs into the flash scope.

```js
var map = {
	addressData = rc.address,
	userID = securityService.getUserID(),
	loggedIn = now()
};

// put all the variables in flash scope as single items
flash.putAll(map);

// Use them later
if( flash.get("loggedIn") ){

}
```

###### remove()
```js
any remove(string name, [boolean saveNow='false'])
```

Remove an object from the temporary flash scope so when the flash scope is serialized it will not be serialized. If you would like to remove a key from the flash scope and make sure your changes are reflected in the persistence storage immediately, use the saveNow argument.
```js
// mark object for removal
flash.remove('notice');

// mark object and remove immediately from flash storage
flash.remove('notice',true);
```
###### removeFlash()
Remove the entire flash storage. We recommend using the clearing methods instead.

###### saveFlash()
Save the flash storage immediately. This process looks at the temporary request flash scope and serializes if it needs to and persists to the correct flash storage on demand.

> **Info** We would advice to not overuse this method as some storage scopes might have delays and serializations

```js
flash.saveFlash();
```

###### size()
Get the number of the items in flash scope

```js
You have #flash.size()# items in your cart!
```

###### More Examples
```js
// handler code:
function saveForm(event){
	// save post
	flash.put("notice","Saved the form baby!");
	// relocate to another event
	setNextEvent('user.show');
}
function show(event){
	// Nothing to do with flash, inflating by flash object automatically
	event.setView('user.show');
}

// User/show.cfm template using if statements
<cfif flash.exists("notice")>
	<div class="notice">#flash.get("notice")#</div>
</cfif>

// User/show.cfm using defaults
<div class="notice">#flash.get(name="notice",default="")</div>
```

