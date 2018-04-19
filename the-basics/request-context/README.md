# Request Context

On every request to a ColdBox event, the framework creates an object that models the incoming request. This object is called the Request Context object \(`coldbox.system.web.context.RequestContext`\) and it contains the incoming FORM/URL/REMOTE variables the client sent in and the object lives in the ColdFusion request scope. You will use this object in the controller and view layer of your application to get/set values, get metadata about the request, marshall data for RESTful request, and so much more.

![](/full/images/RequestCollectionDataBus.jpg)

## How Does It Work

The framework will merge the incoming URL/FORM/REMOTE variables into a single structure called the request collection structure \(**RC**\) that will live inside the request context object. We also internally create a second collection called the private request collection \(**PRC**\) that is useful to store data and objects that have no outside effect.

> **Info** As best practice, store data in the private collection and leave the request collection intact with the client's request data.

The request collection lives in the `request` scope and can be accessed from every single part of the framework's life cycle, except the Model layer of course. Therefore, there is one contract and way on how to handle FORM/URL/REMOTE and any other kind of variables. You can consider the request collection to be sort of a super information highway that is unique per request. You will interact with it to get/set values that any part of a request's lifecycle will interact with it. The ColdBox debugger even traces for you how this data structure changes as events are fired during a request.

> **Note** `FORM` variables take precedence

## What Can I Do With It?

The event object has a plethora of methods to help you deal with a request. We suggest looking at the [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for further inspection.

* Getting a reference to the collection
* Appending structures to the collection
* Removing values from the collection
* Paraming values in the collection \(Similar to cfparam\)
* Getting values from the collection
* Setting values into the collection
* Getting metadata about a request, such as the current executing event, handler, action, layouts, view, and much more.
* Determining a coldbox proxy request
* Determining if you are in SES mode
* Building links for you
* So much more.

## Most Commonly Used Methods

Below you can see a listing of the mostly used methods in the request context object. Please note that when interacting with a collection you usually have an equal **private** collection method.

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
//Get a reference
<cfset var rc = event.getCollection()>

//test if this is an MVC request or a remote request
<cfif event.isProxyRequest()>
  <cfset event.setValue('message', 'We are in proxy mode right now')>
</cfif>

//param a variable called page
<cfset event.paramValue('page',1)>
//then just use it
<cfset event.setValue('link','index.cfm?page=#rc.page#')>

//get a value with a default value
<cfset event.setvalue('link','index.cfm?page=#event.getValue('page',1)#')>

//Set the view to render
<cfset event.setView('homepage')>

//Set the view to render with no layout
<cfset event.setView('homepage',true)>

//set the view to render with caching stuff
<cfset event.setview(name='homepage',cache='true',cacheTimeout='30')>

//override a layout
<cfset event.setLayout('Layout.Ajax')>

//check if a value does not exists
<cfif not event.valueExists('username')>

</cfif>

//Tell the framework to stop processing gracefully, no renderings
<cfset event.noRender()>

//Build a link
<form action="#event.buildLink('user.save')#" method="post">
</form>
```

Please see the online [API Docs](http://apidocs.ortussolutions.com/coldbox/current) for the latest methods and arguments.

### Request Metadata Methods

* `getCurrentAction()` : Get the current execution action \(method\)
* `getCurrentEvent()` : Get's the current incoming event, full syntax.
* `getCurrentHandler()` : Get the handler or handler/package path.
* `getCurrentLayout()` : Get the current set layout for the view to render.

  \*\*`getCurrentView()` : Get the current set view 

* `getCurrentModule()` : The name of the current executing module
* `getCurrentRoutedNamespace()` : The current routed URL mapping namespace if found.
* `getCurrentRoutedURL()` : The current routed URL if matched.
* `getDebugpanelFlag()` : Get's the boolean flag if the ColdBox debugger panel will be rendered.
* `getDefaultLayout()` : Get the name of the default layout.
* `getDefaultView()` : Get the name of the default view.



