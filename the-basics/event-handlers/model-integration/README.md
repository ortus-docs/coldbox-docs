# Model Integration

We have a complete section dedicated to the [Model Layer](../../models/), but we wanted to review a little here since event handlers need to talk to the model layer all the time. By default, you can interact with your models from your event handlers in two ways:

* Dependency Injection \([Aggregation](https://atomicobject.com/resources/oo-programming/object-oriented-aggregation)\)
* Request, use and discard model objects \([Association](https://en.wikipedia.org/wiki/Association_%28object-oriented_programming%29)\)

ColdBox offers its own dependency injection framework, [WireBox](https://wirebox.ortusbooks.com), which allows you, by convention, to talk to your model objects. However, ColdBox also allows you to connect to third-party dependency injection frameworks via the _IOC_ module: [http://forgebox.io/view/cbioc](http://forgebox.io/view/cbioc)

{% hint style="info" %}
Aggregation differs from ordinary composition in that it does not imply ownership. In composition, when the owning object is destroyed, so are the contained objects. - **wikipedia**
{% endhint %}

## Dependency Injection

![](../../../.gitbook/assets/eventhandlerinjection.jpg)

Your event handlers can be **autowired** with dependencies from [WireBox](https://wirebox.ortusbooks.com) by convention. By autowiring dependencies into event handlers, they will become part of the life span of the event handlers \(**singletons**\), since their references will be injected into the handler's `variables` scope.   This is a huge performance benefit since event handlers are wired with all necessary dependencies upon creation instead of requesting dependencies \(usage\) at runtime. We encourage you to use injection whenever possible.

{% hint style="danger" %}
**Warning** As a rule of thumb, inject only **singletons** into **singletons**.  If not you can create unnecessary [scope-widening injection](https://wirebox.ortusbooks.com/advanced-topics/providers/scope-widening-injection) issues and memory leaks.
{% endhint %}

You will achieve this in your handlers via `property` injection, which is the concept of defining properties in the component with a special annotation called `inject`, which tells WireBox what reference to retrieve via the [WireBox Injection DSL](./#injection).  Let's say we have a **users** handler that needs to talk to a model called **UserService**. Here is the directory layout so we can see the conventions

{% code title="Directory Layout" %}
```text
+ handlers
  + users.cfc
+ models
  + UserService.cfc
```
{% endcode %}

Here is the event handler code to leverage the injection:

{% code title="users.cfc" %}
```javascript
component name="MyHandler"{
    
    // Dependency injection of the model: UserService -> variables.userService
    property name="userService" inject="UserService";

    function index( event, rc, prc ){
        prc.data = userService.list()
        event.setView( "users/index" );
    }

}
```
{% endcode %}



Notice that we define a `cfproperty` with a name and `inject` attribute.  The `name` becomes the name of the variable in the `variables` scope and the `inject` annotation tells WireBox what to retrieve.  By default it retrieves model objects by name and path.

{% hint style="success" %}
**Tip:** The [injection DSL](./#injection) is vast and elegant.  Please refer to it.  Also note that you can create object aliases and references in your [config binder](https://wirebox.ortusbooks.com/configuration/configuring-wirebox): `config/WireBox.cfc`
{% endhint %}

## Requesting Model Objects

![](../../../.gitbook/assets/eventhandlermodelrequested.jpg)

The other approach to integrating with model objects is to request and use them as [associations](http://en.wikipedia.org/wiki/Association_%28object-oriented_programming%29) via the framework super type method: `getInstance()`, which in turn delegates to WireBox's `getInstance()` method.  We would recommend requesting objects if they are **transient** \(have state\) objects or stored in some other volatile storage scope \(session, request, application, cache, etc\). Retrieving of objects is okay, but if you will be dealing with mostly **singleton** objects or objects that are created only once, you will gain much more performance by using injection.

{% code title="users.cfc" %}
```javascript
component{

    function index( event, rc, prc ){
        // Request to use the user service, this would be best to inject instead 
        // of requesting it.
        prc.data = getInstance( "UserService" ).list();
        event.setView( "users/index" );
    }
    
    function save( event, rc, prc ){
        // request a user transient object, populate it and save it.
        prc.oUser = populateModel( getInstance( "User" ) );
        userService.save( prc.oUser );
        relocate( "users/index" );
    }

}
```
{% endcode %}

{% hint style="info" %}
**Association** defines a relationship between classes of objects that allows one object instance to cause another to perform an action on its behalf. - 'wikipedia'
{% endhint %}

## A practical example

In this practical example we will see how to integrate with our model layer via WireBox, injections, and also requesting the objects. Let's say that we have a service object we have built called `FunkyService.cfc` and by convention we will place it in our applications `models` folder.

{% code title="Directory Layout" %}
```javascript
 + application
  + models
     + FunkyService.cfc
```
{% endcode %}

**FunkyService.cfc**

```javascript
component singleton{

    function init(){
        return this;
    }

    function add(a,b){ 
        return a+b; 
    }

    function getFunkyData(){
        var data = [
            {name="Luis", age="33"},
            {name="Jim", age="99"},
            {name="Alex", age="1"},
            {name="Joe", age="23"}
        ];
        return data;
    }

}
```

Our funky service is not that funky after all, but it is simple. How do we interact with it? Let's build a Funky event handler and work with it.

### Injection

```javascript
component{

    // Injection via property
    property name="funkyService" inject="FunkyService";

    function index(event,rc,prc){

        prc.data = funkyService.getFunkyData();

        event.renderData( data=prc.data, type="xml" );
    }    


}
```

By convention, I can create a **property** and annotate it with an `inject` attribute. ColdBox will look for that model object by the given name in the `models` folder, create it, persist it, wire it, and return it. If you execute it, you will get something like this:

```markup
<array>
    <item>
        <struct>
            <name>Luis</name>
            <age>33</age>
        </struct>
    </item>
    <item>
        <struct>
            <name>Jim</name>
            <age>99</age>
        </struct>
    </item>
    <item>
        <struct>
            <name>Alex</name>
            <age>1</age>
        </struct>
    </item>
    <item>
        <struct>
            <name>Joe</name>
            <age>23</age>
        </struct>
    </item>
</array>
```

Great! Just like that we can interact with our model layer without worrying about creating the objects, persisting them, and even wiring them. Those are all the benefits that dependency injection and model integration bring to the table.

#### Alternative wiring

You can use the value of the `inject` annotation in several ways. Below is our recommendation.

```javascript
// Injection using the DSL by default name/id lookup
property name="funkyService" inject="FunkyService";
// Injection using the DSL id namespace
property name="funkyService" inject="id:FunkyService";
// Injection using the DSL model namespace
property name="funkyService" inject="model:FunkyService";
```

### Requesting

Let's look at the requesting approach. We can either use the following approaches:

**Via Facade Method**

```javascript
component{

    function index(event,rc,prc){

        prc.data = getInstance( "FunkyService" ).getFunkyData();

        event.renderData( data=prc.data, type="xml" );
    }    


}
```

**Directly via WireBox:**

```javascript
component{

    function index(event,rc,prc){

        prc.data = wirebox.getInstance( "FunkyService" ).getFunkyData();

        event.renderData( data=prc.data, type="xml" );
    }    


}
```

Both approaches do exactly the same thing. In reality `getInstance()` does a `wirebox.getInstance()` callback \(Uncle Bob\), but it is a facade method that is easier to remember. If you run this, you will also see that it works and everything is fine and dandy. However, the biggest difference between injection and usage can be seen with some practical math:

```javascript
1000 Requests made to users.index

- Injection: 1000 handler calls + 1 model creation and wiring call = 1001 calls
- Requesting: 1000 handler calls + 1000 model retrieval + 1 model creation call = 2002 calls
```

As you can see, the best performance is due to injection as the handler object was wired and ready to roll, while the requested approach needed the dependency to be requested. Again, there are cases where you need to request objects such as transient or volatile stored objects.

