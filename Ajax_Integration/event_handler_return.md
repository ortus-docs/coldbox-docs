# Event Handler Return

Any event handler can return HTML data by just returning the HTML from its action function. Then you can just call the event from any AJAX enabled library as a GET or POST.

```js
function sayHello(){
	return "<h1>Say Hello</h1>";	
}

function loginForm(){
	return renderView("security/loginForm");
}
```

