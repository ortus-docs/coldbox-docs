# Contacts Handler

Let's put all the layers together and make the handler talk to the model. I create different saving approaches, to showcase different integration techniques:

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
		var contact = populateModel("Contact");
		// validate it
		var vResults = validateModel(contact);
		// Check it
		if( vResults.hasErrors() ){
			getPlugin("MessageBox").error(messageArray=vResults.getAllErrors());
			return newContact(event,rc,prc);
		}
		else{
			getPlugin("MessageBox").info("Contact created!");
			setNextEvent("contacts");
		}
	}

	function save(event,rc,prc){
		event.paramValue("contactID",0);
		var contact = populateModel( contactService.get( rc.contactID ) );
		// validate it
		var vResults = validateModel(contact);
		// Check it
		if( vResults.hasErrors() ){
			getPlugin("MessageBox").error(messageArray=vResults.getAllErrors());
			return newContact(event,rc,prc);
		}
		else{
			getPlugin("MessageBox").info("Contact created!");
			setNextEvent("contacts");
		}
	}


}
```

