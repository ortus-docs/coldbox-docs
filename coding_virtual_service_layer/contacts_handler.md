# Contacts Handler

That's right, go to the handler now, no need of data layers or services, we build them for you! 

```bash
coldbox create handler name=contacts actions=index,editor,delete,save
```

Now spice it up

```js
/**
* I am a new handler
*/
component{

	// Inject a virtual service layer binded to the contact entity
	property name="contactService" inject="entityService:Contact";

	function index(event,rc,prc){
		prc.contacts = contactService.list(sortOrder="lastName",asQuery=false);
		event.setView("contacts/index");
	}

	function editor(event,rc,prc){
		event.paramValue("id",0);
		prc.contact = contactService.get( rc.id );
		event.setView("contacts/editor");
	}

	function delete(event,rc,prc){
		event.paramValue("id",0);
		contactService.deleteByID( rc.id );
		flash.put( "notice", "Contact Removed!" );
		setNextEvent("contacts");
	}

	function save(event,rc,prc){
		event.paramValue("id",0);
		var contact = populateModel( contactService.get( rc.id ) );
		var vResults = validateModel( contact );
		if( !vResults.hasErrors() ){
			contactService.save( contact );
			flash.put( "notice", "Contact Saved!" );
			setNextEvent("contacts");
		}
		else{
			getPlugin("MessageBox").error(messageArray=vResults.getAllErrors());
			return editor(event,rc,prc);
		}
	}

}
```