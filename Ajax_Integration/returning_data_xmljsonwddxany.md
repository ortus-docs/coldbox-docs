# Returning DATA (XML/JSON/WDDX/ANY)

The ColdBox [RequestContext](http://wiki.coldbox.org/wiki/RequestContext.cfm) has an awesome method: `event.renderData()` that was built specifically for you to interact remotely with the framework and return data either via Ajax or RESTful requests. Our Layouts and Views guide goes in depth about `renderdata()`. This method can produce for you:

* XML
* JSON
* JSONP
* WDDX
* PDF
* TEXT
* HTML
* Custom

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
//jsonp marshalling
function getUsersJSON(event,rc,prc){
	var qUsers = getUserService().getUsers();
	event.renderData(type="jsonp",data=qUsers,jsonCallback="myCallback");
}


// restful handler
function list(event,rc,prc){
	event.paramValue("format","html");

	rc.data = userService.list();

	switch(rc.format){
		case "json": "jsont" : "xml" { 
			event.renderData(type=rc.format,data=rc.data);
			break;	
		}
		default: { 
			event.setView("users/list");
		}
	}

}

// simple tests
function data(event,rc,prc){
	var data = {
		name = "ColdBox", awesome = true, ratings = [5,5,4,3]
	};

	event.renderData(data=data,type="json");
}

// from content and with pdfArgs
function pdf(event,rc,prc){
  var pdfArgs = { bookmark = "yes", backgroundVisible = "yes", orientation="landscape" };
  event.renderData(data=renderView("views/page"), type="PDF", pdfArgs=pdfArgs);
}
```
