# Contact.cfc

An object that represents a contact and self-validates using [ColdBox Validation Module](http://wiki.coldbox.org/wiki/Validation.cfm), and is an awesome [ActiveEntity](http://wiki.coldbox.org/wiki/ORM:ActiveEntity.cfm) object. Let's use CommandBox to build it:

```bash
coldbox create orm-entity entityName=contacts primaryKey=contactID properties=firstName,lastName,email --activeEntity --open
```

Then spice it up with the validation constraints

```js
/**
* A cool Contact entity
*/
component persistent="true" table="contacts" extends="coldbox.system.orm.hibernate.ActiveEntity"{

	// Primary Key
	property name="contactID" fieldtype="id" column="contactID" generator="native" setter="false";
	
	// Properties
	property name="firstName" ormtype="string";
	property name="lastName" ormtype="string";
	property name="email" ormtype="string";
	
	// validation
	this.constraints = {
		firstName = {required=true},
		lastName = {required=true},
		email = {required=true, type="email"}
	};
	
}
```

