# Contact.cfc

An object that represents a contact and self-validates using [ColdBox Validation Module](https://github.com/coldbox-modules/cbox-validation/wiki), and is an awesome [ActiveEntity](https://coldbox.ortusbooks.com/the-basics/models/coding-activeentity-style) object. Let's use CommandBox to build it:

```bash
coldbox create orm-entity entityName=contact primaryKey=contactID properties=firstName,lastName,email --activeEntity --open
```

Then spice it up with the validation constraints

```javascript
/**
* A cool Contact entity
*/
component persistent="true" table="contacts" extends="cborm.models.ActiveEntity"{

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

