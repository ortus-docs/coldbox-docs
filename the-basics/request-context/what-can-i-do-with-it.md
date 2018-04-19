# What Can I Do With It?

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

