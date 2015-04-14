# Global Parameters

|Argument|Type|Required|Default|Description|
|--|--|--|--|--|
|type|string|true|HTML|The type of data to render. Valid types are JSON, JSONP, JSONT, XML, WDDX, PLAIN/HTML, TEXT. The deafult is HTML or PLAIN. If an invalid type is sent in, this method will throw an error|
|data|any|false|---|The data you would like to marshall and return by the framework|
|contentType|string|false|---|The content type of the data. This will be used in the cfcontent tag: text/html, text/plain, text/xml, text/json, etc. The default value is text/html. However, if you choose JSON this method will choose application/json, if you choose WDDX or XML this method will choose text/xml for you. The default encoding is utf-8|
|encoding|string|false|utf-8 |The default character encoding to use|
|statusCode |numeric|false|200|The HTTP status code to send to the browser. |
|statusText |string|false|---|Explains the HTTP status code sent to the browser|
|location |string|false|---|Optional argument used to set the HTTP Location header|
|formats |string or list|false|---|The formats list or array that ColdBox should respond to using the passed in data argument. You can pass any of the valid types (JSON,JSONP,JSONT,XML,WDDX,PLAIN,HTML,TEXT,PDF). For PDF and HTML we will try to render the view by convention based on the incoming event.|
|formatsView|string|false|*implicit event*|The view that should be used for rendering HTML/PLAIN/PDF. By default ColdBox uses the name of the event as an implicit view.|