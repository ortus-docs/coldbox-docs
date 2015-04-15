# The Event Handlers

The event handlers that you will produce for remote interaction are exactly the same as your other handlers, with the exception that they have a return type and return data back to the caller; our proxies. Then our proxies can strong type the return data elements:

Handler:

```js
function getCachekEys(event,rc,prc){
	return getColdBoxOCM( rc.cacheProvider ).getKeys();
}

function listUsers(event,rc,prc){
	prc.data = userService.list();

	if( event.isProxyRequest() ){
		return prc.data;
	}
	
	event.setView("users/listUsers");
}
```

Proxy:
```js
array function getCachekEys(string cacheProvider='default'){
	arguments.event = "proxy.getCacheKeys";
	return process(argumentCollection=arguments);
}

string function getUsersJSON(){
	arguments.event = "proxy.listUsers";
	return serializeJSON( process(argumentCollection=arguments) );
}
```

