# Rendering Data
Every event handler controller can render data back to its caller in several formats: views/layouts, return data and <code>event.renderdata()</code>.  Out of the box ColdBox can marshall data (structs,queries,arrays,complex or even ORM entities) into the following output formats:

* XML
* JSON
* JSONP
* HTML
* TEXT
* WDDX
* PDF
* Custom

## Handler Return Data
Handlers can return simple strings or complex objects. If they return simple strings then the strings will be rendered out to the user:

```js
function index(event,rc,prc){
	return "<h1>Hello from my handler today at :#now()#</h1>";
}

function jsondata( event, rc, prc ){
    var mydata = myservice.getData();
    return serializeJSON( mydata );
}
```

Complex objects in normal MVC mode will not be rendered out.  They only make sense when you are calling the action via a <code>runEvent()</code> call or from a ColdBox Proxy.

```js
function list( event, rc, prc ){
	prc.users = userService.list();
	if( event.isProxyRequest() ){
		return prc.users;
	}
	event.setView( "users/list" );
}
```

## event.renderData()

Using the <code>renderdata()</code> method of the event object is the most flexible for RESTFul web services or pure data marshalling.  Out of the box ColdBox can marshall data (structs,queries,arrays,complex or even ORM entities) into the following output formats:

* XML
* JSON
* JSONP
* HTML
* TEXT
* PDF
* WDDX
* CUSTOM

Below are a few examples:

```js
// xml marshalling
function getUsersXML(event,rc,prc){
	var qUsers = getUserService().getUsers();
	event.renderData(type="XML",data=qUsers);
}
//json marshalling
function getUsersJSON(event,rc,prc){
	var qUsers = getUserService().getUsers();
	event.renderData(type="json",data=qUsers);
}

// restful handler with various formats
function list(event,rc,prc){
	rc.data = userService.list();
    event.renderData( data=rc.data, formats="json,jsont,xml,html,pdf" );
}

// Various formats with specific views
event.renderData(data=MyData, formats="xml,json,html,pdf", formatsView="data/MyView");
```

As you can see, it is very easy to render data back to the browser or caller. You can even choose plain and send HTML back if you wanted too. You can also render out PDF's from ColdBox using the render data method. The data argument can be either the full binary of the PDF or simple values to be rendered out as a PDF, like views, layouts, strings, etc.

```js
// from binary
function pdf(event,rc,prc){
  var binary = fileReadAsBinary( file.path );
  event.renderData(data=binary,type="PDF");
}

// from content
function pdf(event,rc,prc){
  event.renderData(data=renderView("views/page"), type="PDF");
}
```

There is also a **pdfArgs** argument in the render data method that can take in a structure of name-value pairs that will be used in the <code>cfdocument</code> ([See docs](http://help.adobe.com/en_US/ColdFusion/9.0/CFMLRef/WSc3ff6d0ea77859461172e0811cbec22c24-7c21.html)) tag when generating the PDF. This is a great way to pass in arguments to really control the way PDF's are generated uniformly.


```js
// from content and with pdfArgs
function pdf(event,rc,prc){
  var pdfArgs = { bookmark = "yes", backgroundVisible = "yes", orientation="landscape" };
  event.renderData(data=renderView("views/page"), type="PDF", pdfArgs=pdfArgs);
}
```











