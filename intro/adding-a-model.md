# Adding A Model

![](/images/MVC.png)


Let's complete our saga into MVC by developing the **M**, which stands for [model](https://en.wikipedia.org/wiki/Domain_model).  This layer is all your business logic, queries, external dependencies, etc. of your application, which represents the problem to solve or the domain to solve.  This layer is controlled by [WireBox](https://wirebox.ortusbooks.com), the dependency injection framework within ColdBox, which will give you the flexiblity of wiring your objects and persisting them for you.

Let's create a simple contact listing, so open up CommandBox and issue the following command:

```bash
coldbox create model name="ContactService" methods="getAll" persistence="singleton"
```

This will create a `models/ContactService.cfc` with a `getAll()` method and a companion unit test at `tests/specs/unit/ContactServiceTest.cfc`.  Let's open the model object:

```js
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

Let's mock an array of contacts so we can display them later. We can move this to a SQL call later.

```js
component singleton accessors="true"{

    ContactService function init(){
        variables.data = [
            { id=1, name="coldbox" },
            { id=2, name="superman" },
            { id=3, name="batman" }
        ];    
        return this;

    }

    function getAll(){
        return variables.data;
    }

}
```

## Wiring Up Models

We have now created our model so let's tell our event handler about it. Let's create a new handler using CommandBox:

```bash
coldbox create handler name="contacts" actions="index"
```

This will create the `handler/contacts.cfc` handler with an `index()` action, the `views/contacts/index.cfm` view and the accompanying integration test `tests/specs/integration/contactsTest.cfc`.  Let's open the handler and add a new `property` that will have a reference to our model.

```js
component{ 

    property name="contactService" inject="ContactService";

    any function index( event, rc, prc ){ 
        event.setView( "contacts/index" ); 
    }
}
```

Please note that `inject` annotation on the `property` definition.  This tells WireBox what model to inject.  By convention it looks in the `models` folder for the value, which in our case is `ContactService`.  Now let's call it and place some data in the private request collection `prc` so our views can use it.

```js
any function index( event, rc, prc ){
    prc.aContacts = contactService.getAll();
    event.setView( "contacts/index" );
}
```

## Presenting Data

Now that we have put the array of contacts into the `prc` struct as `aContacts`, let's display it to the screen using ColdBox's HTML Helper. 

The ColdBox HTML Helper is a companion class that exists in all layouts and views that allows you to generate semantic HTML5 without the needed verbosity of nesting, or binding to ORM/Business objects.

> **Note** Please check out the API Docs to discover the HTML Helper: http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/core/dynamic/HTMLHelper.html

Open the `contacts/index.cfm` and add the following to the view:

```html
<cfoutput>
<h1>My Contacts</h1>

#html.table( data=prc.aContacts, class="table table-striped"#
</cfoutput>
```

That's it! Execute the event: `http://localhost:{port}/contacts/index` and view the nice table of contacts being presented to you.  Congratulations, you have comed to a complete MVC circle!


![](/images/request-lifecycle.png)

