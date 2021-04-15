# Super Type Usage Methods



![ColdBox Major Classes UML](../../.gitbook/assets/coldboxmajorclasses.jpg)

The super type offers 2 methods for interacting with your model layer:

* `getInstance()` - Retrieve a model object \(Instead of injection\)
* `populateModel()` - Retrieve and/or populate a model object from the request collection.

Please also note that your models do not inherit from anything within ColdBox. They are shy and decoupled by default.  If you need anything from the ColdBox environment, then you will have to inject it using our [injection dsl.](injection-dsl/)

## `getInstance()`

Here is the signature

```javascript
/**
 * Get a instance object from WireBox
 *
 * @name The mapping name or CFC path or DSL to retrieve
 * @initArguments The constructor structure of arguments to passthrough when initializing the instance
 * @dsl The DSL string to use to retrieve an instance
 *
 * @return The requested instance
 */
function getInstance( name, initArguments={}, dsl )
```

**Examples**

```javascript
// Retrieve the User.cfc in the model folder
var oUser = getInstance('User');
// Retrieve the User.cfc in the model/users folder
var oUser = getInstance("users.User")
// Retrieve the User using an alias you mapped in your configuration binder
var oUser = getInstance("MyUser");
// Retrieve an object using a full instantation path
var oUtil = getInstance("mypath.utilities.MyUtil");
```

## `populateModel()`

ColdBox can populate or bind model objects from data in the request collection by matching the name of the form element to the name of a property on the object. You can also populate model objects from JSON, XML, Queries and other structures a-la-carte by talking directly to [WireBox's object populator](https://wirebox.ortusbooks.com/advanced-topics/wirebox-object-populator).

```javascript
/**
 * Populate a model object from the request Collection or a passed in memento structure
 *
 * @model The name of the model to get and populate or the acutal model object. If you already have an instance of a model, then use the populateBean() method
 * @scope Use scope injection instead of setters population. Ex: scope=variables.instance.
 * @trustedSetter If set to true, the setter method will be called even if it does not exist in the object
 * @include A list of keys to include in the population
 * @exclude A list of keys to exclude in the population
 * @ignoreEmpty Ignore empty values on populations, great for ORM population
 * @nullEmptyInclude A list of keys to NULL when empty
 * @nullEmptyExclude A list of keys to NOT NULL when empty
 * @composeRelationships Automatically attempt to compose relationships from memento
 * @memento A structure to populate the model, if not passed it defaults to the request collection
 * @jsonstring If you pass a json string, we will populate your model with it
 * @xml If you pass an xml string, we will populate your model with it
 * @qry If you pass a query, we will populate your model with it
 * @rowNumber The row of the qry parameter to populate your model with
 *
 * @return The instance populated
 */
function populateModel(
	required model,
	scope="",
	boolean trustedSetter=false,
	include="",
	exclude="",
	boolean ignoreEmpty=false,
	nullEmptyInclude="",
	nullEmptyExclude="",
	boolean composeRelationships=false,
	struct memento=getRequestCollection(),
	string jsonstring,
	string xml,
	query qry
)
```

**Examples**:

```javascript
var user = ormService.populate( ormService.new("User"), data );

// populate with includes only
var user = ormService.populate( ormService.new("User"), data, "fname,lname,email" );

//populate with excludes
var user = ormService.populate(target=ormService.new("User"),memento=data,exclude="id,setup,total" );

// populate with null values when value is empty string
var user = ormService.populate(target=ormService.new("User"),memento=data,nullEmptyInclude="lastName,dateOfBirth" );

// populate many-to-one relationship
var data = {
    firstName = "Luis",
    role = 1 // "role" is the name of the many-to-one relational property, and one is the key value
};
var user = ormService.populate( target=ormService.new("User"), memento=data, composeRelationships=true );
// the role relationship will be composed, and the value will be set to the appropriate instance of the Role model

// populate one-to-many relationship
var data = {
    firstName = "Luis",
    favColors = "1,2,3" ( or [1,2,3] ) // favColors is the name of the one-to-many relational property, and 1, 2 and 3 are key values of favColor models
};
var user = ormService.populate( target=ormService.new("User"), memento=data, composeRelationships=true );
// the favColors property will be set to an array of favColor entities

// only compose some relationships
var data = {
    firstName = "Luis",
    role = 1,
    favColors = [ 1, 3, 19 ]
};
var user = ormService.populate( target=ormService.new("User"), memento=data, composeRelationships=true, exclude="favColors" );
// in this example, "role" will be composed, but "favColors" will be excluded
```

