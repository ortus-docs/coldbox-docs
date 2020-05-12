# URL Routing

Every module has access to it's own routing object and the main application's router object:

* `router` : The module's router object
* `appRouter` : The parent's router object

The module router is used to register URL routes that will be directed for execution into the router.  The `appRouter` can be used to register URL routes that can be directed ANYWHERE in the application.

{% hint style="info" %}
All routing is the same for all ColdBox applications, so just refer to the [Routing](../../the-basics/routing/) section for much more information.
{% endhint %}

## Module URL Entry Point

All modules will automatically register an entry point URL pattern via the `this.entryPoint` setting. If an incoming URL has that specific pattern, then it will search for the module's routing table for a match instead of the global URL routing table.

{% code title="ModuleConfig.cfc" %}
```javascript
this.entryPoint = "/mymodule";
```
{% endcode %}

You can also register 1 or many entry points for modules via the parent router using the `toModuleRouting()` method.  You can do this via the `appRouter` in your module itself or in the main application's router file: `config/Router.cfc`

{% code title="config/Router.cfc" %}
```javascript
route( "/myModule" ).toModuleRouting( "myModule" );
route( "/myAlias" ).toModuleRouting( "myModule" );
```
{% endcode %}

## Module Router

The module's routing table can be composed automatically for you by convention if you have a `config/Router.cfc` or you can load them manually in your `configure()` method via the `router` object.

{% code title="myModule/config/Router.cfc" %}
```javascript
component {

	function configure() {
		route( "/", "echo.index" );

		route( "/:handler/:action" ).end();
	}

}
```
{% endcode %}

{% code title="myModule/ModuleConfig.cfc" %}
```javascript
component{

    function configure(){
         
        router.route( "/", "echo.index" );
        router.route( "/:handler/:action" ).end();   
        
        approuter.route( "/hello", "myModule:echo.index" );
    }

}
```
{% endcode %}



