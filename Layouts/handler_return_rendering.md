# Handler Return Rendering

Handlers can also return content by simply returning a simple string from the action method. How that string is built is up to you, it can be a simple string, a call from a model CFC, or inline rendering that we will see below.

```js
component name="general"{

	function index(event,rc,prc){
		return "<h1>Hello from my handler today at :#now()#</h1>";
	}

}
```

That's it, just return the simple content and it will sent to the output buffer. This technique is great for producing viewlets/widgets or self-sustainable events, where you can call and render events from within anywhere in ColdBox via the runEvent() method. Please refer to the viewlets section in this guide.