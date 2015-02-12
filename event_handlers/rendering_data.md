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