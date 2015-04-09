# Contact.cfc

An object that represents a contact and self-validates using [ColdBox Validation](http://wiki.coldbox.org/wiki/Validation.cfm):

```js
component accessors="true"{
	
	// properties
	property name="firstName";
	property name="lastName";
	property name="email";

	// validation
	this.constraints = {
		firstName = {required=true},
		lastName = {required=true},
		email = {required=true, type="email"}
	};

	function init(){
		return this;
	}

}
```

