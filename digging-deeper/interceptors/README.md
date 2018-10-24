# Interceptors

Interceptors are CFC listeners that react on incoming events. Events can be announced by the core framework or be custom events from your application. These interceptors can also be stacked to form interceptor chains that can be executed implicitly for you. This is a powerful feature that can help developers and framework contributors share and interact with their work. \(Read more on [Intercepting Filters](http://www.corej2eepatterns.com/Patterns2ndEd/InterceptingFilter.htm)\)   


![](https://raw.githubusercontent.com/ortus-docs/coldbox-docs/master/full/images/InterceptorChain.gif)

## Event Driven Programming

The way that interceptors are used is usually referred to as event-driven programming, which can be very familiar if you are already doing any JavaScript or AngularJS coding. You can listen and execute intercepting points anywhere you like in your application, you can even produce content whenever you announce:

**Custom Announcements**

```markup
<!-- Announce an event in the div and produce content -->
<div>#announceInterception( 'onSidebar' )#</div>

// Announce inside a function
if( userCreated ){
    announceInterception( 'onUserCreate', { user = prc.oUser } );
}
```

![](https://raw.githubusercontent.com/ortus-docs/coldbox-docs/master/full/images/eventdriven.jpg)

If you are familiar with design patterns, custom interceptors can give you an implementation of observer/observable listener objects, much like any event-driven system can provide you. In a nutshell, an observer is an object that is registered to listen for certain types of events, let's say as an example `onError` is a custom interception point and we create a CFC that has this `onError` method. Whenever in your application you announce or broadcast that an event of type onError occurred, this CFC will be called by the ColdBox interceptor service.

**Interceptor Example**

```javascript
component extends="coldbox.system.Interceptor"{

    function onError( event, interceptData={} ){
        // Listen to onError events
    }

}
```

### Resources

* [http://en.wikipedia.org/wiki/Observer\_pattern](http://en.wikipedia.org/wiki/Observer_pattern)
* [http://sourcemaking.com/design\_patterns/observer](http://sourcemaking.com/design_patterns/observer)
* [http://java.sun.com/blueprints/corej2eepatterns/Patterns/InterceptingFilter.html](http://java.sun.com/blueprints/corej2eepatterns/Patterns/InterceptingFilter.html)

### For what can I use them

Below are just a few applications of ColdBox Interceptors:

* Security
* Event based Security
* Method Tracing
* AOP Interceptions
* Publisher/Consumer operations
* Implicit chain of events
* Content Appending or Pre-Pending
* View Manipulations
* Custom SES support
* Cache advices on insert and remove
* Much much more...

  \*

