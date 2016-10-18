# Output Buffer Argument

All interception methods receive the request buffer as a third argument so you interceptors can produce output elegantly if they do not extend from the core system Interceptor. This is great for interceptors like the module configuration objects or simple objects that are registered as interceptors.

```js
function onSidebar(event, interceptData, buffer){
	savecontent variable="local.data"{
		/// HTML HERE
	}

	arguments.buffer.append( local.data );
}


// using argument
function preRender(event,interceptData,buffer){
	//clear all of it first, just in case.
	arguments.buffer.clear();
	//Append to buffer
	arguments.buffer.append('<h1>This software is copyright by Funky Joe!</h1>');	
}
```

Some common methods of the request buffer are:

* append(str) Append strings to the buffer
* clear() Clear the entire buffer
* length() Get size of the buffer
* getString() Get the entire string in the buffer
* getBufferObject() Get the actual java String Builder object

Like with anything in ColdBox, please visit the [API DOCS](http://www.apidocs.coldbox.org/) for the latest methods and arguments available.