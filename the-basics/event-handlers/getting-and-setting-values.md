# Getting & Setting Values

We all need values in our applications. That is why we interact with the [request context](../request-context.md) in order to place data from our model layer into it so our views can display it, or to retrieve data from a user's request. You will either interact with the event object to get/set values or put/read values directly via the received `rc` and `prc` references.

![RC/PRC Data Super Highway](../../.gitbook/assets/requestcollectiondatabus%20%281%29%20%281%29.jpg)

{% hint style="warning" %}
We would recommend you use the private request collection \(`prc`\) for setting manual data and using the standard request collection \(`rc`\) for reading the user's unsafe request variables. This way a clear distinction can be made on what was sent from the user and what was set by your code.
{% endhint %}

```javascript
//set a value for views to use
event.setValue( "name", "Luis" );
event.setPrivateValue( "name", "Luis" );

// retrieve a value the user sent
event.getValue( "name" );
// retrieve a value the user sent or give me a default value
event.getValue( "isChecked", false );

// retrieve a private value
event.getPrivateValue( "name" );
// retrieve a private value or give me a default value
event.getPrivateValue( "isChecked", false );

// param a value
event.paramValue( "user_id", "" );
// param a private value
event.paramPrivateValue( "user_id", "" );

// remove a value
event.removeValue( "name" );
// remove a private value
event.removePrivateValue( "name" );

//check if value exists
if( event.valueExists( "name" ) ){

}
//check if private value exists
if( event.privateValueExists( "name" ) ){

}

// set a view for rendering
event.setView( 'blog/index' );

// set a layout for rendering
event.setLayout( 'main' );

// set a view and layout
event.setView( view="blog/userinfo", layout="ajax" );
```

{% hint style="danger" %}
**Important** The most important paradigm shift from procedural to an MVC framework is that you NO LONGER will be talking to URL, FORM, REQUEST or any ColdFusion scope from within your handlers, layouts, and views. The request collection already has URL, FORM, and REQUEST scope capabilities, so leverage it.
{% endhint %}

