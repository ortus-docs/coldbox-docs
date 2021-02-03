# Request Context

On every request to a ColdBox event, the framework creates an object that models the incoming request. This object is called the **Request Context Object**\(`coldbox.system.web.context.RequestContext`\), it contains the incoming **FORM/REMOTE/URL** variables the client sent in and the object lives in the ColdFusion `request` scope and you will use to for responses and interacting with client data.

{% hint style="info" %}
Please visit the latest [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for further information about the request context.
{% endhint %}

This object contains two structures internally:

1. `RC` - The Request Collection which contains the **FORM/REMOTE/URL** data merged into a single structure.  This is considered to be **unsafe** data as it comes from any request.
2. `PRC` - The Private Request Collection which is a structure that can be used to safely store sensitive data.  This structure cannot be modified from the outside world.

{% hint style="info" %}
The order of preference of variables when merged is **FORM** first then **REMOTE** then **URL**.

**REMOTE** variables are from leveraging the [ColdBox Proxy.](../digging-deeper/coldbox-proxy/)
{% endhint %}

You will use these objects in the controller and view layer of your application to get/set values, get metadata about the request, generate URLs, transform data for RESTful requests, and so much more. It is the glue that binds the controller and view layer together. As we progress in the guides, you will progress in mastering the request context.

![RC/PRC Data Super Highway](../.gitbook/assets/requestcollectiondatabus%20%281%29%20%281%29.jpg)

{% hint style="danger" %}
Note that there is no model layer in the diagram. This is by design; the model will receive data from the handlers/interceptors directly.
{% endhint %}

## Most Commonly Used Methods

Below you can see a listing of the most commonly used methods in the request context object. Please note that when interacting with a collection you usually have an equal **private** collection method.

* _buildLink\(\)_ : Build a link in SES or non SES mode for you with tons of nice abstractions.
* _clearCollection\(\)_ : Clears the entire collection
* _collectionAppend\(\)_ : Append a collection overwriting or not
* _getCollection\(\)_ : Get a reference to the collection
* _getEventName\(\)_ : The event name in use in the application \(e.g. do, event, fa\)
* _getSelf\(\)_ : Returns index.cfm?event=
* _getValue\(\)_ : get a value
* _getTrimValue\(\)_ : get a value trimmed
* _isProxyRequest\(\)_ : flag if the request is an incoming proxy request
* _isSES\(\)_ : flag if ses is turned on
* _isAjax\(\)_ : Is this request ajax based or not
* noRender\(boolean\) : flag that tells the framework to not render any html, just process and silently stop.
* _overrideEvent\(\)_ : Override the event in the collection
* _paramValue\(\)_: param a value in the collection
* _removeValue\(\)_ : remove a value
* _setValue\(\)_ : set a value
* _setLayout\(\)_ : Set the layout to use for this request
* _setView\(\)_ : Used to set a view to render
* _valueExists\(\)_ : Checks if a value exists in the collection.
* _renderData\(\)_ : Marshall data to JSON, JSONP, XML, WDDX, PDF, HTML, etc.

Some Samples:

```javascript
// test if this is an MVC request or a remote request
if ( event.isProxyRequest() ){
  event.setValue('message', 'We are in proxy mode right now');
}

// param a variable called page
event.paramValue('page',1);
// then just use it
event.setValue('link','index.cfm?page=#rc.page#');

// get a value with a default value
event.setvalue('link','index.cfm?page=#event.getValue('page',1)#');

// Set the view to render
event.setView('homepage');

// Set the view to render with no layout
event.setView('homepage',true);

// set the view to render with caching stuff
event.setview(name='homepage',cache='true',cacheTimeout='30');

// override a layout
event.setLayout('Layout.Ajax');

// check if a value does not exists
if ( !event.valueExists('username') ) {

}

// Tell the framework to stop processing gracefully, no renderings
event.noRender();

// Build a link
<form action="#event.buildLink( 'user.save' )#" method="post">
</form>
```

Please see the online [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for the latest methods and arguments.

## Request Metadata Methods

* `getCurrentAction()` : Get the current execution action \(method\)
* `getCurrentEvent()` :  Get the current incoming event, full syntax.
* `getCurrentHandler()` : Get the handler or handler/package path.
* `getCurrentLayout()` : Get the current set layout for the view to render.
* `getCurrentView()` : Get the current set view
* `getCurrentModule()` : The name of the current executing module
* `getCurrentRoutedNamespace()` : The current routed URL mapping namespace if found.
* `getCurrentRouteRecord()` : Get the current routed record used in resolving the event
* `getCurrentRouteMeta()` : Get the current routed record metdata struct
* `getCurrentRoutedURL()` : The current routed URL if matched.
* `getDefaultLayout()` : Get the name of the default layout.
* `getDefaultView()` : Get the name of the default view.

