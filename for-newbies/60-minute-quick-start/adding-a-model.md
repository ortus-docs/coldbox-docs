# Adding A Model

![](../../.gitbook/assets/mvc%20%281%29.png)

Let's complete our saga into MVC by developing the **M**, which stands for [model](https://en.wikipedia.org/wiki/Domain_model). This layer is all your business logic, queries, external dependencies, etc. of your application, which represents the problem to solve or the domain to solve. 

### WireBox

This layer is controlled by [WireBox](https://wirebox.ortusbooks.com), the dependency injection framework within ColdBox, which will give you the flexibility of wiring your objects and persisting them for you.

### Creating A Service Model

Let's create a simple contact listing, so open up CommandBox and issue the following command:

```bash
coldbox create model name="ContactService" methods="getAll" persistence="singleton"
```

This will create a `models/ContactService.cfc` with a `getAll()` method and a companion unit test at `tests/specs/unit/ContactServiceTest.cfc`. Let's open the model object:

```javascript
component singleton accessors="true"{      

    ContactService function init(){         
        return this;     
    }

    function getAll(){      

    }

}
```

{% hint style="warning" %}
Notice the `singleton` annotation on the component tag. This tells WireBox that this service should be cached for the entire application life-span. If you remove the annotation, then the service will become a _transient_ object, which means that it will be re-created every time it is requested.
{% endhint %}

### Add Some Data

Let's mock an array of contacts so we can display them later. We can move this to a SQL call later.

```javascript
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

{% hint style="success" %}
We also have created a project to mock any type of data: [MockDataCFC](https://www.forgebox.io/view/mockdatacfc).  Just use CommandBox to install it: `install mockdatacfc` 

You can then leverage it to mock your contacts or any simple/complex data requirement.
{% endhint %}

### Wiring Up The Model To a Handler

We have now created our model so let's tell our event handler about it. Let's create a new handler using CommandBox:

```bash
coldbox create handler name="contacts" actions="index"
```

This will create the `handler/contacts.cfc` handler with an `index()` action, the `views/contacts/index.cfm` view and the accompanying integration test `tests/specs/integration/contactsTest.cfc`. 

Let's open the handler and add a new ColdFusion `property` that will have a reference to our model object.

```javascript
component{ 

    property name="contactService" inject="ContactService";

    any function index( event, rc, prc ){ 
        event.setView( "contacts/index" ); 
    }
}
```

Please note that `inject` annotation on the `property` definition. This tells WireBox what model to inject into the handler's `variables`scope.  

By convention it looks in the `models` folder for the value, which in our case is `ContactService`. Now let's call it and place some data in the private request collection `prc` so our views can use it.

```javascript
any function index( event, rc, prc ){
    prc.aContacts = contactService.getAll();
    event.setView( "contacts/index" );
}
```

### Presenting The Data

Now that we have put the array of contacts into the `prc` struct as `aContacts`, let's display it to the screen using ColdBox's HTML Helper.

The ColdBox HTML Helper is a companion class that exists in all layouts and views that allows you to generate semantic HTML5 without the needed verbosity of nesting, or binding to ORM/Business objects.

{% hint style="info" %}
Please check out the API Docs to discover the HTML Helper: [http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/core/dynamic/HTMLHelper.html](http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/core/dynamic/HTMLHelper.html)
{% endhint %}

Open the `contacts/index.cfm` and add the following to the view:

```markup
<cfoutput>
<h1>My Contacts</h1>

#html.table( data=prc.aContacts, class="table table-striped" )#
</cfoutput>
```

That's it! Execute the event: `http://localhost:{port}/contacts/index` and view the nice table of contacts being presented to you. 

Congratulations, you have made a complete **MVC** circle!

![](../../.gitbook/assets/request-lifecycle%20%281%29.png)

> **Tip** You can find much more information about models and dependency injection in our [full docs](https://coldbox.ortusbooks.com/content/full/models/)

