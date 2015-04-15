# Using Flash RAM

There are several ways to interact with the ColdBox Flash RAM:

* Using the flash scope object (Best Practice)
* Using the persistVariables() method from the super type and coldbox controller (coldbox.system.web.Controller)
* Using the persistence arguments in the setNextEvent() method from the super type and coldbox controller (coldbox.system.web.Controller)

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

######

######

######

######

######

######

######

######

######

######

######

######

