# Adding A Model

Let's complete our saga into MVC by developing the **M**, which stands for model.  This layer is all your business logic, queries, external dependencies, etc. of your application, which represents the problem to solve or the domain to solve.  This layer is controlled by WireBox, the dependency injection framework within ColdBox, which will give you the flexiblity of wiring your objects and persisting them for you.

Let's create a simple contact listing, so open up CommandBox and issue the following command:

```bash
coldbox create model name="ContactService" methods="getAll" persistence="singleton"
```

This will create a `models/ContactService.cfc` and a companion unit test at `tests/specs/unit/ContactServiceTest.cfc`.  Let's open the model object:

```js
/**
* I am a new Model Object
*/
component singleton accessors="true"{      

    ContactService function init(){         
        return this;     
    }

    function getAll(){      

    }

}
```

Notice the `singleton` annotation on the component tag.  This tells WireBox that this service should be cached for the entire application life-span. If you remove the annotation, then the service will become a _transient_ object, which means that it will be re-created every time it is requested.

## Add Some Data

Let's mock an array of contacts so we can display them later.

```js
/**
* I am a new Model Object
*/

component singleton accessors="true"{

    ContactService function init(){
    
         return this;

    }



 function getAll(){


 }


}
```
