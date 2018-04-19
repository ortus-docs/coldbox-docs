# Contacts Handler

That's right, go to the handler now, no need of data layers or services, we build them for you! This time, we show you the entire CRUD operations as Active Entity makes life easy!

```bash
coldbox create handler name=contacts actions=index,editor,delete,save
```

Then spice it up

```javascript
/**
* I am a new handler
*/
component{

    function index(event,rc,prc){
        prc.contacts = entityNew("Contact").list(sortOrder="lastName",asQuery=false);
        event.setView("contacts/index");
    }

    function editor(event,rc,prc){
        event.paramValue("id",0);
        prc.contact = entityNew("Contact").get( rc.id );
        event.setView("contacts/editor");
    }

    function delete(event,rc,prc){
        event.paramValue("id",0);
        entityNew("Contact").deleteByID( rc.id );
        flash.put( "notice", "Contact Removed!" );
        setNextEvent("contacts");
    }

    function save(event,rc,prc){
        event.paramValue("id",0);
        var contact = populateModel( entityNew("Contact").get( rc.id ) );
        if( contact.isValid() ){
            contact.save();
            flash.put( "notice", "Contact Saved!" );
            setNextEvent("contacts");
        }
        else{
            flash.put( "errors", contact.getValidationResults().getAllErrors() );
            return editor(event,rc,prc);
        }
    }

}
```

