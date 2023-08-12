---
description: Easily bind incoming data into your models.
---

# Model Data Binding

ColdBox allows you to populate data from `FORM/URL/REMOTE/XML/JSON/Struct`  into your model objects by convention. This is done via [WireBox's object population](https://wirebox.ortusbooks.com/advanced-topics/wirebox-object-populator) capabilities.&#x20;

### populate()

The super type has a method called `populate` which you will use to trigger the data binding.  The supported incoming data sources are:

* request collection **RC**
* `structs`
* `json`
* `xml`
* `query`

The method will try to match the incoming variables names in the data packet to a `setter` or `property` in the target model object.  If there is a match, then it will inject that data into the model or call it's `setter` for you.

Let's explore the API

```cfscript

/**
 * Populate an object from the incoming request collection
 *
 * @model                The name of the model to get and populate or the acutal model object. If you already have an instance of a model, then use the populateBean() method
 * @scope                Use scope injection instead of setters population. Ex: scope=variables.instance.
 * @trustedSetter        If set to true, the setter method will be called even if it does not exist in the object
 * @include              A list of keys to include in the population
 * @exclude              A list of keys to exclude in the population
 * @ignoreEmpty          Ignore empty values on populations, great for ORM population
 * @nullEmptyInclude     A list of keys to NULL when empty
 * @nullEmptyExclude     A list of keys to NOT NULL when empty
 * @composeRelationships Automatically attempt to compose relationships from memento
 * @memento              A structure to populate the model, if not passed it defaults to the request collection
 * @jsonstring           If you pass a json string, we will populate your model with it
 * @xml                  If you pass an xml string, we will populate your model with it
 * @qry                  If you pass a query, we will populate your model with it
 * @rowNumber            The row of the qry parameter to populate your model with
 * @ignoreTargetLists    If this is true, then the populator will ignore the target's population include/exclude metadata lists. By default this is false.
 *
 * @return The instance populated
 */
function populate(
	required model,
	scope                        = "",
	boolean trustedSetter        = false,
	include                      = "",
	exclude                      = "",
	boolean ignoreEmpty          = false,
	nullEmptyInclude             = "",
	nullEmptyExclude             = "",
	boolean composeRelationships = false,
	struct memento               = getRequestCollection(),
	string jsonstring,
	string xml,
	query qry,
	boolean ignoreTargetLists = false
)
```

### Examples

Let's do a quick example.  Here is a `Person.cfc` that has two properties with automatic getters/setters.

**Person.cfc**

{% code title="models/Person.cfc" lineNumbers="true" %}
```cfscript
component accessors="true"{

    property name="name";
    property name="email";

    function init(){
        setName('');
        setEmail('');
    }
}
```
{% endcode %}

**editor.cfm**&#x20;

Here is an editor to submit a form to create the person.

{% code title="views/person/editor.cfm" lineNumbers="true" %}
```javascript
<cfoutput>
<h1>Funky Person Form</h1>
#html.startForm( action='person.save' )#

    #html.textfield( label="Your Name:", name="name", wrapper="div")#
    #html.textfield( label="Your Email:", name="email", wrapper="div")#

    #html.submitButton( value="Save" )#

#html.endForm()#
</cfoutput>
```
{% endcode %}

**Event Handler -> person.cfc**

Here is an event handler to do the saving

{% code title="handlers/person.cfc" lineNumbers="true" %}
```cfscript
component{

    function editor(event,rc,prc){
        event.setView( "person/editor" );        
    }

    function save(event,rc,prc){

        var person = populateModel( "Person" );

        writeDump( person );
        abort;
    }

}
```
{% endcode %}

Run the code, and you will see that the populator matched the incoming variables into the model and thus binding it.

### ORM Integration

The populator can also bind ORM objects natively.  However, it can also build out relationships via the `composeRelationships` argument.  This allows you to create the objects from simple identifiers for the following relationships:

* `one-to-one` : The id of the relationship
* `many-to-one` : The id of the parent relationship
* `one-to-many` : A list of identifiers to link to the target object which ends up being an array of objects
* `many-to-many` : A list of identifiers to link to the target object which ends up being an array of objects

So if we have a `User` object with a `many-to-one` of `Role` and a `many-to-many` of `Permissions`, we could populate the `User` with all of it's data graph:

{% code title="user.json" lineNumbers="true" %}
```javascript
{
   "fname" : "luis",
   "lname" : "coldbox",
   "role" : 123,
   "permissions" : "123,456,789"
}
```
{% endcode %}

This packet has the two relationships `role` and `permissions` as an incoming list.  The list can be a simple string or an actual JSON array.  To populate we can do this:

```cfscript
populate( model : "User", composeRelationships : true )
```

That's it. ColdBox will take care of matching and building out the relationships.

{% hint style="info" %}
You can also use the `nullEmptyInclude` and `nullEmptyExclude` properties to include and exclude `null` value population.

You can also use `ignoreEmpty` to ignore all the empty values on population so the ORM treats them as `null`
{% endhint %}

### Mass Population Control: `this.population`

ColdBox also supports the ability for target objects to dictate how they will be populated.  This convention allows for objects to encapsulate the way the mass population of data is treated. This way, you don’t have to scrub or pass include excludes lists via population arguments; it can all be nicely encapsulated in the targeted objects.  Just declare a `this.population` in your object's pseudo constructor:

{% code lineNumbers="true" %}
```javascript
this.population = {
    include : [ "firstName", "lastName", "username", "role" ],
    exclude : [ "id", "password", "lastLogin" ]
};
```
{% endcode %}

The populator will look for a `this.population` struct with the following keys:

* `include` : an array of property names to allow population **ONLY**
* `exclude` : an array of property names to **NEVER** allow population

#### Ignoring Mass Population

The population methods also have an argument called: `ignoreTargetLists` which defaults to `false`, meaning it inspects the objects for these population markers. If you pass it as `true` then the markers will be ignored. This is great for the population of objects from queries or an array of structs or mementos that **YOU** have control of.  Great in DAOs or service layers.\`

```cfscript
populateFromStruct(
    target : userService.newUser(),
    memento : record,
    ignoreTargetLists : true
}
```

