# Viewlets - Reusable Events

A viewlet is a self sufficient view or a widget that can live on its own, its data is pre-fetched and can just be renderer anywhere in your system.

What in the world is this? Well, imagine a portal, in which each section of the portal is self-sufficient, with controls and data. You don't want to call all the handlers for this data for every single piece of content. It's not efficient, you need to create a separation. Well, a viewlet is such a separation that provides you with the ability to create reusable events. So how do we achieve this?

1. You will use the method `runEvent()` anywhere you want a viewlet to be displayed or the content rendered. This calls an internal event that will be in charge to prepare and render the viewlet.
2. Create the portable event but make sure it returns the produced content.

## **Simple Example**

```javascript
<div id="leftbar">
#runEvent( event='viewlets.userinfo', eventArguments={ userID=4 } )#
</div>
```

This code just renders out the results of a `runEvent()` method call. Please note that you can pass in arguments to the event using the `eventArguments` argument.  This makes the event act like a method call with arguments to ti.  Remember that all events you call via `runEvent()` will share the same RC/PRC.

{% hint style="info" %}
I would suggest you look at [the API docs](http://apidocs.ortussolutions.com/coldbox/current) to discover all arguments to the `runEvent()` method call.
{% endhint %}

### **Event Code**

{% code title="viewlets.cfc" %}
```javascript
function userinfo( event, rc, prc, userID=0 ){
    
    // place data in prc and prefix it to avoid collisions
    prc.userinfo_qData = userService.getUserInfo( arguments.userID );

    // render out content 
    return renderView( "viewlets/userinfo" );
}
```
{% endcode %}

As you can see from the code above, the handler signature can accept arguments which are passed via the `eventArguments` structure. It talks to a service layer and place some data on the private request collection the viewlet will use. It then returns the results of a `renderView()` call that will render out the exact viewlet I want. You can be more creative and do things like:

* render a layout + view combo
* render data
* return your own custom strings
* etc

{% hint style="warning" %}
**Caution** We would suggest you namespace or prefix your private request collection variables for viewlets in order to avoid collisions from multiple viewlet events in the same execution thread or instead pass the necessary arguments into a view via the `args` argument.
{% endhint %}

### **View Code**

{% code title="viewlets/userinfo.cfm" %}
```markup
<cfoutput>
    <div>User Info Panel</div>
    <div>Username: #prc.userinfo_qData.username#</div>
    <div>Last Login: #prc.userinfo_qData.lastLogin#</div>
</cfoutput>
```
{% endcode %}

The view is a normal standard view, it doesn't even know it is a viewlet, remember, views are DUMB!

## Content Variables

A content variable is a variable that contains HTML/XML or any kind of visual content that can easily be rendered anywhere. So instead of running the viewlet event in the view, you can abstract it to the controller layer and assign the output to a content variable:

```javascript
function home(event,rc,prc){

    // render some content variables with funky arguments
    prc.sideColumn = renderView(view='tags/sideColumn',cache=true,cacheTimeout=10);

    // set view
    event.setView('general/home');
}
```

So how do I render it?

```javascript
<div id="content">
  <div id="leftColumn">
  <cfoutput>#prc.sideColumn#</cfoutput>
  </div>

  <div id="mainView">
  <cfoutput>#renderView()#</cfoutput>
  </div>
</div>
```

Another example, is what if we do not know if the content variable will actually exist? How can we do this? Well, we use the event object for this and its magic getValue\(\) method.

```javascript
<div id="content">
  <div id="leftColumn">
  <cfoutput>#prc.sideColumn ?: ''#</cfoutput>
  </div>

  <div id="mainView">
  <cfoutput>#renderView()#</cfoutput>
  </div>
</div>
```

So now, if no content variable exists, an empty string will be rendered.

{% hint style="danger" %}
**Important** String manipulation in Java relies on immutable structures, so performance penalities might ensue. If you will be doing a lot of string manipulation, concatenation or rendering, try to leverage native java objects: StringBuilder or StringBuffer
{% endhint %}

