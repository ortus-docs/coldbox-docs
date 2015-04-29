# Distinguishing Request Types

Now, what if you want to distinguish between a normal request and a proxy request? Well, the request context object, most commonly known as the event object has a method called:

* `isProxyRequest` : This boolean method determines what type of request is being executed.

```js
function listUsers(event,rc,prc){
	prc.data = userService.list();

	if( event.isProxyRequest() ){
		return prc.data;
	}
	
	event.setView("users/listUsers");
}
```




