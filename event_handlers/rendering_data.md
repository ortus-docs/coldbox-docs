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

Using the <code>renderdata()</code> method of the event object is the most flexible for RESTFul web services or pure data marshalling.

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

// restful handler
function list(event,rc,prc){
	rc.data = userService.list();
    event.renderData( data=rc.data, formats="json,jsont,xml,html,pdf" );
}
```




