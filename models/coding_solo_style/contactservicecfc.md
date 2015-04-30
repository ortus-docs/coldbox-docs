# ContactService.cfc

Here is our service layer and we have added some logging just for fun :). Notice that this object is a singleton and has some dependency injection.

```
coldbox create model name=Contact --open
```

Then spice it up

```js
component accessors="true"{
	
	// Dependency Injection
	property name="dao" inject="ContactDAO";
	property name="log" inject="logbox:logger:{this}";
	property name="populator" inject="wirebox:populator";
	property name="wirebox" inject="wirebox";

	function init(){
		return this;
	}

	/**
	* Get all contacts as an array of objects or query
	*/
	function list(boolean asQuery=false){
		var q = dao.getAllUsers();
		log.info("Retrieved all contacts", q.recordcount);
		
		if( asQuery ){ return q; }

		// convert to objects
		var contacts = [];
		for(var x=1; x lte q.recordcount; x++){
			arrayAppend( contacts, populator.populateFromQuery( wirebox.getInstance("Contact"), q, x ) );
		}

		return contacts;
	}

	/**
	* Get a persisted contact by ID or new one if 0 or no records
	*/
	function get(required contactID=0){
		var q = dao.getContact(arguments.contactID);
		// if 0 or no records
		if( contactID eq 0 OR q.recordcount eq 0 ){
			// return a new object
			return wirebox.getInstance("Contact");
		}
		// Else return the object
		return populator.populateFromQuery( wirebox.getInstance("Contact"), q, 1 );
	}

	... ALL OTHER METHODS HERE  ....

}
```

Now, some observations of the code:

* We use the populator object that is included in [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) to make our lives easier so we can populate objects from queries and deal with objects. 
* We also inject a reference to the object factory [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) so it can create Contact objects for us. Why? Well what if those objects had dependencies as well.