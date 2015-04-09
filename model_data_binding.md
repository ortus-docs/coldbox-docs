# Model Data Binding

[WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) also offers you the capability to bind incoming FORM/URL/REMOTE data into your model objects by convention. The easiest approach is to use our populateModel() function call:

```js
populateModel(any model, [any scope=''], [any<Boolean> trustedSetter='false'], [any include=''], [any exclude=''])
```

This will try to match incoming variable names to setters or properties in your domain objects and then populate them for you.

|Argument|Type|Required|Default|Description|
|--|--|--|--|--|
|model|any|true|---|The name of the model to get and populate or the actual model object reference.|
|scope|string|false||Use scope injection instead of setters population. Ex: scope=variables.instance.|
|trustedSetter|boolean|false|false|If set to true, the setter method will be called even if it does not exist in the model object|
|include|string|false||A list of keys to include in the population|
|exclude|string|false||A list of keys to exclude in the population|

Let's do a quick sample:

Person.cfc 

```js
component accessors="true"{

	property name="name";
	property name="email";
	
	function init(){
		setName('');
		setEmail('');
	}
}
```

Then here is our form (using our awesome HTML helper):

editor.cfm 

```js
<cfoutput>
<h1>Funky Person Form</h1>
#html.startForm(action='person.save')#

	#html.textfield(label="Your Name:",name="name",wrapper="div")#
	#html.textfield(label="Your Email:",name="email",wrapper="div")#
	
	#html.submitButton(value="Save")#

#html.endForm()#
</cfoutput>
```

And our event handler to process it:

person.cfc 

```js
component{
	
	function editor(event,rc,prc){
		event.setView("person/editor");		
	}
	
	function show(event,rc,prc){
		
		var person = populateModel("Person");
		
		writeDump(person);abort;
	}

}
```

In the dump you will see that the *name* and *email* properties have been binded, cool it works.