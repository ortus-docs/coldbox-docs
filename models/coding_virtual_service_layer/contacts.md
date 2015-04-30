# Contact.cfc

Let's use CommandBox to build it:

```bash
coldbox create orm-entity entityName=contacts primaryKey=contactID properties=firstName,lastName,email --open
```

Then spice it up with the validation constraints

```js
/**
* A cool Contact entity
*/
component persistent="true" table="contacts"{

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

