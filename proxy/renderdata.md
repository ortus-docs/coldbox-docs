# RenderData()

The ColdBox Proxy also has the ability to use the request context's `renderData()` method. So you can build a system that just uses this functionality to transform data into multiple requests. Even have the ability for the same handler to respond to REST/SOAP and MVC all in one method:

**Event Handler:**
```js
function list(event,rc,prc){
	prc.data = userService.list();
    event.renderData( data=prc.users, formats="xml,json,html" );
}
```

**Proxy: **
```js
string function getUsers(string format="json"){
	validateFormat( arguments.format );
	arguments.event = "users.list";
	return process(argumentCollection=arguments);
}
```
This handler can now respond to HTMl requests, SOAP requests, Flex/Air Requests and even RESTFul requests. How about that!

