# URL Routing

The ColdBox Routing DSL will be used to register routes for your application, which exists in your application or module router object.  Routing takes place using several methods inside the router, which are divided into the following categories:

1. **Initiatiors** - Starts a pattern registration, but does not fully register the route until a terminator is called.
2. **Modifiers** - Modifies the pattern with extra metdata to listen to.
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

* `header( name, value, overwrite=true )` - attach a response header if the route matches
* `headers( map, overwrite=true )` - attach multiple response headers if the route matches
* `as( name )` - Register the route as a named route
* `rc( name, value, overwrite=true )` - Add an `RC` value if the route matched
* `rcAppend map, overwrite=true )` - Add multiple values to the `RC` collection if the route matched
* `prc( name, value, overwrite=true )` - Add an `PRC` value if the route matched
* `prcAppend map, overwrite=true )` - Add multiple values to the `PRC` collection if the route matched
* `withHandler( handler )` - Map the route to execute a handler
* `withAction( action )` - Map the route to execute a single action or a struct that represents verbs and actions
* `withModule( module )` - Map the route to a module
* `withNamespace( namespace )` - Map the route to a namespace
* `withSSL() `- Force SSL
* `withCondition( condition )` - Apply a runtime closure/lambda enclosure
* `withDomain( domain )` - Map the route to a domain or subdomain
* `withVerbs( verbs )` - Restrict the route to listen to only these HTTP Verbs
* `packageResolver( toggle )` - Turn on/off convention for packages
* `valuePairTranslator( toggle )` - Turn on/off automatic name value pair translations

### Terminators

Terminators finalize the routing process by registering the route in the Router.

* `end()` - Register the route as it exists
* `toView( view, layout, noLayout=false, viewModule, layoutModule )` - Send the route to a view/layout 
* `toRedirect( target, statusCode=301 ) `- Relocate the route to another event
* `to( event )` - Execute the event if the route matches
* `toHandler( handler )` - Execute the handler if the route matches
* `toResponse( body, statusCode=200, statusText="ok" )` - Inline response action
* `toModuleRouting( module )` - Send to the module router for evaluation
* `toNamespaceRouting( namespace )` - Send to the namespace router for evaluation



