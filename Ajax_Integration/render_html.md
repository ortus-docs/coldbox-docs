# Render HTML

You can use the request context's renderdata method to return HTML as well.

```js
function sayHello(){
	event.renderData(data="<h1>Say Hello</h1>");
}

function loginForm(){
	event.renderData(data=renderView("security/loginForm"));
}
```

