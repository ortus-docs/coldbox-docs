# HTTP Method Security

More often you will find that certain web operations need to be restricted in terms of what HTTP verb is used to access a resource. For example, you do not want form submissions to be done via **GET** but via **POST** or **PUT** operations. HTTP Verb recognition is also essential when building strong RESTFul APIs when security is needed as well.

## Manual Solution
You can do this manually, but why do the extra coding :)

```js
function delete(event,rc,prc){
	// determine incoming http method
	if( event.getHTTPMethod() == "GET" ){
		flash.put("notice","invalid action");
		setNextEvent("users.list");
	}
	else{
		// do delete here.
	}
}
```

This solution is great and works, but it is not THAT great. We can do better, how about using those nice before advices? You can but then you have to mess with the except and only lists. This approach is okay and you most likely will have great control on what to do with the invalid request, but there is no uniformity and you still have to write code for it.

## Allowed Methods Property

Another feature property on an event handler is called <code>this.allowedMethods</code>. It is a declarative structure that you can use to determine what the allowed HTTP methods are for any action on the event handler. If the request action HTTP method is not found in the list, it will look for a <code>onInvalidHTTPMethod()</code> (See [Docs](convention_methods.md)) on the handler and call it if found.  Otherwise ColdBox throws a 405 exception that is uniform across requests.

```js
component{

	this.allowedMethods = {
		delete = "POST,DELETE",
		list   = "GET"
	};

	function list(event,rc,prc){
		// list only
	}

	function delete(event,rc,prc){
		// do delete here.
	}
}
```

> **Note** The key in the structure is the name of the action and the value is a list of allowed HTTP methods. If the action is not listed in the structure, then it means allow all HTTP methods. That's it! Just remember to either use the <code>onError()</code> or <code>onInvalidHTTPMethod()</code> method conventions or an exception handler to deal with the security exceptions.

## Allowed Methods Annotation

You can tag your event actions with a `allowedMethods` annotation and add a list of the allowed HTTP verbs as well.  This gives you a nice directed ability right at the function level instead of a property.  It is also useful when leveraging DocBox documentation.

```js
function index( event, rc, prc) allowedMethods="GET,POST"{
}
```



