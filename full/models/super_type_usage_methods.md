# Super Type Usage Methods

![](/images/ColdBoxMajorClasses.jpg)

The super type offers 2 methods for interacting with your model layer:

* `getModel()` - Retrieve a model object (Instead of injection)
* `populateModel()` - Retrieve and/or populate a model object from the request collection.


##getModel()

Here is the signature

```js
/**
* Get a model object
* @name.hint The mapping name or CFC path to retrieve
* @dsl.hint The DSL string to use to retrieve an instance
* @initArguments.hint The constructor structure of arguments to passthrough when initializing the instance
*/
function getModel( name, dsl, initArguments={} ){}
```

**Examples**

```js
// Retrieve the User.cfc in the model folder
var oUser = getModel('User');
// Retrieve the User.cfc in the model/users folder
var oUser = getModel("users.User")
// Retrieve the User using an alias you mapped in your configuration binder
var oUser = getModel("MyUser");
// Retrieve an object using a full instantation path
var oUtil = getModel("mypath.utilities.MyUtil");
```

## populateModel()

ColdBox can populate or bind model objects from data in the request collection by matching the name of the form element to the name of a property on the object. You can also populate model objects from JSON, XML, Queries and other structures a-la-carte by talking directly to [WireBox](http://wirebox.ortusbooks.com/content/wirebox_object_populator/index.html)'s object populator.

```js
/**
* Populate a model object from the request Collection or a passed in memento structure
* @model.hint The name of the model to get and populate or the acutal model object. If you already have an instance of a model, then use the populateBean() method
* @scope.hint Use scope injection instead of setters population. Ex: scope=variables.instance.
* @trustedSetter.hint If set to true, the setter method will be called even if it does not exist in the object
* @include.hint A list of keys to include in the population
* @exclude.hint A list of keys to exclude in the population
* @ignoreEmpty.hint Ignore empty values on populations, great for ORM population
* @nullEmptyInclude.hint A list of keys to NULL when empty
* @nullEmptyExclude.hint A list of keys to NOT NULL when empty
* @composeRelationships.hint Automatically attempt to compose relationships from memento
* @memento A structure to populate the model, if not passed it defaults to the request collection
* @jsonstring If you pass a json string, we will populate your model with it
* @xml If you pass an xml string, we will populate your model with it
* @qry If you pass a query, we will populate your model with it
* @rowNumber The row of the qry parameter to populate your model with
*/
function populateModel()
```

**Examples**:

```js
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


