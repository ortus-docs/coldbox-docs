# Executing Events

Apart from executing events from the URL/FORM or Remote interfaces, you can also execute events internally, either **public** or **private** from within your event handlers or from interceptors, other handlers, layouts or views. You do this by using the `runEvent()` method which is inherited from our `FrameworkSuperType` class. Here is the method signature:

```javascript
/**
* Executes events with full life-cycle methods and returns the event results if any were returned.
* @event The event string to execute, if nothing is passed we will execute the application's default event.
* @prePostExempt If true, pre/post handlers will not be fired. Defaults to false
* @private Execute a private event if set, else defaults to public events
* @defaultEvent The flag that let's this service now if it is the default event running or not. USED BY THE FRAMEWORK ONLY
* @eventArguments A collection of arguments to passthrough to the calling event handler method
* @cache.hint Cached the output of the runnable execution, defaults to false. A unique key will be created according to event string + arguments.
* @cacheTimeout.hint The time in minutes to cache the results
* @cacheLastAccessTimeout.hint The time in minutes the results will be removed from cache if idle or requested
* @cacheSuffix.hint The suffix to add into the cache entry for this event rendering
* @cacheProvider.hint The provider to cache this event rendering in, defaults to 'template'
*/
function runEvent;
```

The interesting aspect of internal executions is that all the same rules apply, so your handlers can return content like widgets, views, or even data. Also, the eventArguments enables you to pass arguments to the method just like method calls:

**Executions**

```javascript
//public event
runEvent( 'users.save' );

//post exempt
runEvent( event='users.save', prePostExempt=true );

//Private event
runEvent( event='users.persist', private=true );

// Run event as a widget
<cfoutput>#runEvent(event='widgets.userInfo',prePostExempt=true,eventArguments={widget=true});

// Run with Caching
runEvent( event="users.list", cache=true, cacheTimeout=30 );
```

**Declaration**

```javascript
// handler responding to widget call
function userInfo( event, rc, prc, widget=false ){

    prc.userInfo = userService.get( rc.id );

    // set or widget render
    if( arguments.widget ){
        return renderView( "widgets/userInfo" );
    }

    // else set view
    event.setView( "widgets/userInfo" );

}
```

