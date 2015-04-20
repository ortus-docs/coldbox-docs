# XML Parameters

|Argument|Type|Required|Default|Description|
|--|--|--|--|--|
|pdfArgs|structure|false|---|All the PDF arguments to pass along to the CFDocument tag.|

The purpose of this method is for you to set the data to return, how to marshal or convert it and what type it is. The framework will then take care of everything for you, no need to abort the request or set a view or layout. The framework morphs into a remote framework:

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
```

As you can see, it is very easy to render data back to the browser or caller. You can even choose plain and send html back if you wanted too. You can also render out PDF's from ColdBox using the render data method. The data argument can be either the full binary of the PDF or simple values to be rendered out as a PDF, like views, layouts, strings, etc.

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

There is also a pdfArgs argument in the render data method that can take in a structure of name-value pairs that will be used in the cfdocument (See docs) tag when generating the PDF. This is a great way to pass in arguments to really control the way PDF's are generated uniformly.

```js
// from content and with pdfArgs
function pdf(event,rc,prc){
  var pdfArgs = { bookmark = "yes", backgroundVisible = "yes", orientation="landscape" };
  event.renderData(data=renderView("views/page"), type="PDF", pdfArgs=pdfArgs);
}
```
