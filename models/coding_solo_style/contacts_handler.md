# Contacts Handler

Let's put all the layers together and make the handler talk to the model. I create different saving approaches, to showcase different integration techniques:

```
coldbox create handler name=Contacts actions=index,newContact,create,save --open
```

Spice it up now

```js
component{
	
	// Dependency Injection
	property name="contactService" inject="ContactService";

	function index(event,rc,prc){
		// Get all contacts
		prc.aContacts = contactService.list();
		event.setView("contacts/index");
	}

	function newContact(event,rc,prc){
		event.setView("contacts/newContact");
	}

	function create(event,rc,prc){
		var contact = populateModel( "Contact" );
		// validate it
		var vResults = validateModel( contact );
		// Check it
		if( vResults.hasErrors() ){
			flash.put( "errors", vResults.getAllErrors() );
			return newContact(event,rc,prc);
		}
		else{
			flash.put( "notice", "Contact Created!" );
			setNextEvent("contacts");
		}
	}

	function save(event,rc,prc){
		event.paramValue("contactID",0);
		var contact = populateModel( contactService.get( rc.contactID ) );
		// validate it
		var vResults = validateModel( contact );
		// Check it
		if( vResults.hasErrors() ){
			flash.put( "errors", vResults.getAllErrors() );
			return newContact(event,rc,prc);
		}
		else{
			flash.put( "notice", "Contact Saved!" );
			setNextEvent("contacts");
		}
	}
}
```

Now that you have finished, go execute the contacts and interact with it.
