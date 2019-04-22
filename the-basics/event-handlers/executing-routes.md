# Executing Routes

A part from using `runEvent()` to execute events, you can also abstract it by using the `runRoute()` method.  This method is fairly similar but with the added benefit of executing a NAMED route instead of the direct event it represents.  This gives you the added flexibility of abstracting the direct event and leveraging the named route.

All the same feature of `runEvent()` apply to `runRoute()`

## RunRoute\(\)

Just like you can create links based on named routes and params, you can execute named routes and params as well internally via `runRoute()`

```javascript
/**
 * Executes internal named routes with or without parameters. If the named route is not found or the route has no event to execute then this method will throw an `InvalidArgumentException`.
 * If you need a route from a module then append the module address: `@moduleName` or prefix it like in run event calls `moduleName:routeName` in order to find the right route.
 * The route params will be passed to events as action arguments much how eventArguments work.
 *
 * @name The name of the route
 * @params The parameters of the route to replace
 * @cache Cached the output of the runnable execution, defaults to false. A unique key will be created according to event string + arguments.
 * @cacheTimeout The time in minutes to cache the results
 * @cacheLastAccessTimeout The time in minutes the results will be removed from cache if idle or requested
 * @cacheSuffix The suffix to add into the cache entry for this event rendering
 * @cacheProvider The provider to cache this event rendering in, defaults to 'template'
 * @prePostExempt If true, pre/post handlers will not be fired. Defaults to false
 *
 * @throws InvalidArgumentException
 */
any function runRoute(
	required name,
	struct params={},
	boolean cache=false,
	cacheTimeout="",
	cacheLastAccessTimeout="",
	cacheSuffix="",
	cacheProvider="template",
	boolean prePostExempt=false
)
```

### Parameters

The params argument you pass to the runRoute\(\) method will be translated into event arguments. Therefore they will be passed as arguments to the event the route represents:

```javascript
// Execute
runRoute( "userData", { id=4 } )
```

In the example above, the `userData` named route points to the `user.data` event.

{% code-tabs %}
{% code-tabs-item title="user.cfc" %}
```javascript
component{

    property name="userService" inject;

    function data( event, rc, prc, id=0 ){
        if( id == 0 )
            return {};
            
        return userService.getData( id );
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Module Routes

If you want to execute module routes, no problem!  Just use our `@ or :` notation to tell the controller from which module's router we should pick the route from.

```javascript
# Using @ destination
runRoute( "userData@user", { id=4 } )

# Using : prefix
runRoute( "user:userData", { id=4 } )
```



