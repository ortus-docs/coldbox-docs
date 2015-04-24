# What Can I Do With It?

The event object has a plethora of methods to help you deal with a request.  We suggest looking at the API Docs for further inspection.

* Getting a reference to the collection
* Appending structures to the collection
* Removing values from the collection
* Paraming values in the collection (Similar to cfparam)
* Getting values from the collection
* Setting values into the collection
* Getting metadata about a request, such as the current executing event, handler, action, layouts, view, and much more.
* Determining a coldbox proxy request
* Determining if you are in SES mode
* Building links for you
* So much more.


### Most Commonly Used Methods

Below you can see a listing of the mostly used methods in the request context object:

* *buildLink()* : Build a link in SES or non SES mode for you with tons of nice abstractions.
* *clearCollection()* : Clears the entire collection
* *collectionAppend()* : Append a collection overwriting or not
* *getCollection()* : Get a reference to the collection
* *getEventName()* : The event name in use in the application (e.g. do, event, fa)
* *getSelf()* : Returns index.cfm?event=
* *getValue()* : get a value
* *getTrimValue()* : get a value trimmed
* *isProxyRequest()* : flag if the request is an incoming proxy request
* *isSES()* : flag if ses is turned on
* *isAjax()* : Is this request ajax based or not
* noRender(boolean) : flag that tells the framework to not render any html, just process and silently stop.
* *overrideEvent()* : Override the event in the collection
* *paramValue()*: param a value in the collection
* *removeValue()* : remove a value
* *setValue()* : set a value
* *setLayout()* : Set the layout to use for this request
* *setView()* : Used to set a view to render
* *showDebugPanel()* : Sets whether the ColdBox debugging panel will be rendered or not.
* *valueExists()* : Checks if a value exists in the collection.
* *renderData()* : Marshall data to JSON, JSONP, XML, WDDX, PDF, HTML, etc.


Some Samples:

```js

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

//Don't show the debug panel for this request
<cfset event.showDebugPanel(false)>

//Tell the framework to stop processing gracefully, no renderings
<cfset event.noRender()>

//Build a link
<form action="#event.buildLink('user.save')#" method="post">
</form>
```

Please see the online [API](http://www.coldbox.org/api)for the latest methods and arguments.

### Request Metadata Methods

* *getCurrentAction()* : Get the current execution action (method)
* *getCurrentEvent()* : Get's the current incoming event, full syntax.
* *getCurrentHandler()* : Get the handler or handler/package path.
* *getCurrentLayout()* : Get the current set layout for the view to render.
** getCurrentView()* : Get the current set view 
* *getCurrentModule()* : The name of the current executing module
* *getCurrentRoutedNamespace()* : The current routed URL mapping namespace if found.
* *getCurrentRoutedURL()* : The current routed URL if matched.
* *getDebugpanelFlag()* : Get's the boolean flag if the ColdBox debugger panel will be rendered.
* *getDefaultLayout()* : Get the name of the default layout.
* *getDefaultView()* : Get the name of the default view.

Please see the online [API](http://www.coldbox.org/api) for the latest methods and arguments.