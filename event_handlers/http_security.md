# HTTP Method Security

More than often you will find that certain web operations needs to be restricted in terms of what HTTP verb is used to access a resource. For example, you do not want form submissions to be done via **GET** but via **POST** or **PUT** operations. HTTP Verb recognition is also essential when building strong RESTFul APIs and security is needed as well.

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

This solution is great and works, but it is not THAT great. We can do better, how about using those nice before advices? You can but then you have to mess with the except and only lists. This approach is ok and you most likely will have great control on what to do with the invalid request, but there is no uniformity and you still have to write code for it.

## Allowed Methods Property

Another feature property on an event handler is called allowedMethods and it is a declarative structure that you can use to determine what are the allowed HTTP methods for any action on the event handler. If the request action HTTP method is not found in the list then it throws a 405 exception that is uniform across requests.