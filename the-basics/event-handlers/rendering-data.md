# Rendering Data

Handler actions can return data back to its callers in different formats

* Complex Data
* HTML
* Rendered Data via `event.renderData()`

## Complex Data

By default, any complex data returned from handler actions will automatically be marshaled to **JSON** by default:

```javascript
function showData( event, rc, prc ){
    prc.data = service.getUsers();
    return prc.data;
}
```

Simple as that. ColdBox detects the complex object and tries to convert it to **JSON** for you automatically.  

### `renderdata` Action Annotation

If you want ColdBox to marshall the content to another type like XML or PDF. Then you can use the `renderdata` annotation on the action itself.  The `renderdata` annotation can be any of the following values:

* json
* jsonp
* jsont
* xml
* html
* text
* pdf

```javascript
function usersAsXML( event, rc, prc ) renderdata=xml{
    prc.data = service.getUsers();
    return prc.data;
}

function usersAsPDF( event, rc, prc ) renderdata=pdf{
    prc.data = service.getUsers();
    return prc.data;
}
```

### `renderdata` Component Annotation

You can also add the renderData annotation to the component definition and this will override the default of JSON. So if you want XML as the default, you can do this:

```javascript
component renderdata="xml"{

}
```

### $renderData Convention

If the returned complex data is an **object** and it contains a function called `$renderData(),` then ColdBox will call it for you automatically.  So instead of marshaling to JSON automatically, your object decides how to marshal itself.

```javascript
function showUser( event, rc, prc ){
    return service.getUser( 2 );
}
```

## Native HTML

By default if your handlers return simple values, then they will be treated as returning HTML.

```javascript
function index(event,rc,prc){
    return "<h1>Hello from my handler today at :#now()#</h1>";
}

function myData( event, rc, prc ){
     prc.mydata = myservice.getData();
     return renderView( "main/myData" );
}
```

## event.renderData\(\)

Using the `renderdata()` method of the **event** object is the most flexible for RESTFul web services or pure data marshaling. Out of the box ColdBox can marshall data \(structs, queries, arrays, complex or even ORM entities\) into the following output formats:

* XML
* JSON
* JSONP
* JSONT
* HTML
* TEXT
* PDF
* WDDX
* CUSTOM

Here is the method signature:

```javascript
/**
* Use this method to tell the framework to render data for you. The framework will take care of marshalling the data for you
* @type The type of data to render. Valid types are JSON, JSONP, JSONT, XML, WDDX, PLAIN/HTML, TEXT, PDF. The deafult is HTML or PLAIN. If an invalid type is sent in, this method will throw an error
* @data The data you would like to marshall and return by the framework
* @contentType The content type of the data. This will be used in the cfcontent tag: text/html, text/plain, text/xml, text/json, etc. The default value is text/html. However, if you choose JSON this method will choose application/json, if you choose WDDX or XML this method will choose text/xml for you.
* @encoding The default character encoding to use.  The default encoding is utf-8
* @statusCode The HTTP status code to send to the browser. Defaults to 200
* @statusText Explains the HTTP status code sent to the browser.
* @location Optional argument used to set the HTTP Location header
* @jsonCallback Only needed when using JSONP, this is the callback to add to the JSON packet
* @jsonQueryFormat JSON Only: This parameter can be a Boolean value that specifies how to serialize ColdFusion queries or a string with possible values "row", "column", or "struct".
* @jsonAsText If set to false, defaults content mime-type to application/json, else will change encoding to plain/text
* @xmlColumnList XML Only: Choose which columns to inspect, by default it uses all the columns in the query, if using a query
* @xmlUseCDATA XML Only: Use CDATA content for ALL values. The default is false
* @xmlListDelimiter XML Only: The delimiter in the list. Comma by default
* @xmlRootName XML Only: The name of the initial root element of the XML packet
* @pdfArgs All the PDF arguments to pass along to the CFDocument tag.
* @formats The formats list or array that ColdBox should respond to using the passed in data argument. You can pass any of the valid types (JSON,JSONP,JSONT,XML,WDDX,PLAIN,HTML,TEXT,PDF). For PDF and HTML we will try to render the view by convention based on the incoming event
* @formatsView The view that should be used for rendering HTML/PLAIN/PDF. By default ColdBox uses the name of the event as an implicit view
* @formatsRedirect The arguments that should be passed to relcoate as part of a redirect for the HTML action.  If the format is HTML and this struct is not empty, ColdBox will call relcoate with these arguments.
* @isBinary Bit that determines if the data being set for rendering is binary or not.
*/
function renderData(
	type="HTML",
	required data,
	contentType="",
	encoding="utf-8",
	numeric statusCode=200,
	statusText="",
	location="",
	jsonCallback="",
 	jsonQueryFormat="true",
	boolean jsonAsText=false,
	xmlColumnList="",
	boolean xmlUseCDATA=false,
	xmlListDelimiter=",",
	xmlRootName="",
	struct pdfArgs={},
	formats="",
	formatsView="",
	formatsRedirect={},
	boolean isBinary=false
){
```

Below are a few simple examples:

```javascript
// html marshalling
function renderHTML(event,rc,prc){
    event.renderData( data="<h1>My HTML</h1>" );
}
// xml marshalling
function getUsersXML(event,rc,prc){
    var qUsers = getUserService().getUsers();
    event.renderData( type="XML", data=qUsers );
}
//json marshalling
function getUsersJSON(event,rc,prc){
    var qUsers = getUserService().getUsers();
    event.renderData( type="json", data=qUsers, statusCode=403 );
}
```

As you can see, it is very easy to render data back to the browser or caller. You can even choose plain and send HTML back if you wanted to.

### Render PDFs

You can also render out PDFs from ColdBox using the render data method. The data argument can be either the full binary of the PDF or simple values to be rendered out as a PDF; like views, layouts, strings, etc.

```javascript
// from binary
function pdf(event,rc,prc){
  var binary = fileReadAsBinary( file.path );
  event.renderData( data=binary, type="PDF" );
}

// from content
function pdf(event,rc,prc){
  event.renderData( data=renderView("views/page"), type="PDF" );
}
```

There is also a `pdfArgs` argument in the render data method that can take in a structure of name-value pairs that will be used in the `cfdocument` \([See docs](http://help.adobe.com/en_US/ColdFusion/9.0/CFMLRef/WSc3ff6d0ea77859461172e0811cbec22c24-7c21.html)\) tag when generating the PDF. This is a great way to pass in arguments to really control the way PDF's are generated uniformly.

```javascript
// from content and with pdfArgs
function pdf(event,rc,prc){
  var pdfArgs = { bookmark = "yes", backgroundVisible = "yes", orientation="landscape" };
  event.renderData(data=renderView("views/page"), type="PDF", pdfArgs=pdfArgs);
}
```

### Renderdata With Formats

The `renderData()` method also has two powerful arguments: `formats & formatsView`. If you currently have code like this:

```javascript
event.paramValue("format", "html");

switch( rc.format ){
    case "json" : case "jsonp" : case "xml" : {
        event.renderData(data=mydata, type=rc.format);
        break;
    } 
    case "pdf" : {
        event.renderData(data=renderView("even/action"), type="pdf");
        break;
    }
    case "html" : {
        event.setView( "event/action" );
        break;
      }
};
```

Where you need to param the incoming format extension, then do a switch and do some code for marshalling data into several formats. Well, no more, you can use our formats argument and ColdBox will marshall and code all that nasty stuff for you:

```javascript
event.renderData( data=MyData, formats="xml,json,html,pdf" );
```

That's it! ColdBox will figure out how to deal with all the passed in formats for you that `renderdata` can use. By convention it will use the name of the incoming event as the view that will be rendered for HTML and PDF; implicit views. If the event was users.list, then the view would be views/users/list.cfm. However, you can tell us which view you like if it is named different:

```javascript
event.renderData( data=MyData, formats="xml,json,html,pdf", formatsView="data/MyView" );
```

If you need to redirect for html events, you can pass any arguments you normally would pass to `setNextEvent` to `formatsRedirect`.

```javascript
event.renderData( data=MyData, formats="xml,json,html,pdf", formatsRedirect={event="Main.index"} );
```

### Custom Data Conversion

You can do custom data conversion by convention when marshalling CFCs. If you pass in a CFC as the `data` argument and that CFC has a method called `$renderdata()`, then the marshalling utility will call that function for you instead of using the internal marshalling utilities. You can pass in the custom content type for encoding as well:

```javascript
// get an instance of your custom converter
myConverter = getInstance("MyConverter")
// put some data in it
myConverter.setData( data );
// marshall it out according to your conversions and the content type it supports
event.renderData( data= myConverter, contentType=myConverter.getContentType() );
```

The CFC converter:

```javascript
component accessors="true"{

    property name="data" type="mytype";
    property name="contentType";

    function init(){ 
        setContentType("text");
        return this; 
    }

    // The magical rendering
    function $renderdata(){
        var d = {
            n = data.getName(),
            a = data.getAge(),
            c = data.getCoo(),
            today = now()
        };

        return d.toString();
    }

}
```

In this approach your `$renderdata()` function can be much more customizable than our internal serializers. Just remember to use the right contentType argument so the browser knows what to do with it.

