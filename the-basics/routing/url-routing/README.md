# URL Routing

The ColdBox Routing DSL will be used to register routes for your application, which exists in your application or module router object.  Routing takes place using several methods inside the router, which are divided in the following categories:

1. **Initiatiors** - Starts a pattern registration, but does not fully register the route until a terminator is called.
2. **Modifiers** - Modifies the pattern with extra metdata to listen to
3. **Terminators** - Finalizes the registration process usually by telling the router what happens when the route pattern is detected.

{% hint style="warning" %}
Please note that order of declaration of the routes is imperative.  Order matters.
{% endhint %}

{% hint style="info" %}
Please remember to check out the latest [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for the latest methods and argument signatures.
{% endhint %}

Let's investigate the methods first and then start the routing process.

### Initiators

The following methods are used to initiate a route registration process. 

{% hint style="danger" %}
Please note that a route will not register unless a terminator is called or the inline target terminator is passed.
{% endhint %}

* `route( pattern, [target], [name] )` - Register a new route with optional **target** terminators and a name
* `get( pattern, [target], [name] )` - Register a new route with optional **target** terminators, a name and a GET http verb restriction
* `post( pattern, [target], [name] )` - Register a new route with optional **target** terminators, a name and a POST http verb restriction
* `put( pattern, [target], [name] )` - Register a new route with optional **target** terminators, a name and a PUT http verb restriction
* `patch( pattern, [target], [name] )` - Register a new route with optional **target** terminators, a name and a PATCH http verb restriction
* `options( pattern, [target], [name] )` - Register a new route with optional **target** terminators, a name and a OPTIONS http verb restriction
* `group( struct options, body )` - Group routes together with options that will be applied to all routes declared in the `body` closure/lambda.

### Modifiers

Modifiers will tell the routing service about certain restrictions, conditions or locations for the routing process.  It will not register the route just yet.

* header\( name, value, overwrite=true \)
* headers\( map, overwrite=true \)
* as\( name \)
* rc\( name, value, overwrite=true \)
* rcAppend map, overwrite=true \)
* prc\( name, value, overwrite=true \)
* prcAppend map, overwrite=true \)
* withHandler\( handler \)
* withAction\( action \)
* withModule\( module \)
* withNamespace\( namespace \)
* withSSL\(\)
* withCondition\( condition \)
* withDomain\( domain \)
* withVerbs\( verbs \)
* packageResolver\(\)
* valuePairTranslator\(\)

### Terminators

Terminators finalize the routing process by registering the route in the Router.

* end\(\)
* toView\( view, layout, noLayout=false, viewModule, layoutModule \)
* toRedirect\( target, statusCode=301 \)
* to\( event \)
* toHandler\( handler \)
* toResponse\( body, statusCode=200, statusText="ok" \)
* toModuleRouting\( module \)
* toNamespaceRouting\( namespace \)



