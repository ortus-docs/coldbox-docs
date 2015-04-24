# Overriding Layouts

Ok, now that we have started to get funky, let's keep going. How can I change the layout on the fly for a specific view? Very easily, using yet another new method from the event object, called `setLayout()`

```js
event.setLayout( name )
```

This is great, so we can change the way views are rendered on the fly programmatically. We can switch the content to a PDF in an instant. So let's do that

```js
function home(event,rc,prc){
	
	if( event.valueExists('print') ){
		event.setLayout('layout.PDF');
	}

	// logic here
	
	// set view
	event.setView('general/home');

}
```

WOW!! That easy! We check if a variable print is sent in and if it is, we override the layout with the event.setLayout() method. Another interesting technique is using some of the implicit execution points of a handler: preHandler() & postHandler(). You can use these implicit events to determine a layout for an entire event handler.

```js
function preHandler(event,action,eventArguments){
	event.setLayout('Layout.PDF');
}
function postHandler(event,action,eventArguments){
	event.setLayout('Layout.PDF');
}
```

But you can even get more creative and decide to either create a preProcess [Interceptor](http://wiki.coldbox.org/wiki/Interceptor.cfm) or a request start handler and listen for certain environment changes and switch layouts. You can then completely take control of how views will be rendered according to maybe browser type, mobile usage, etc.

```js
function requestStartHandler(event,rc,prc){
	if( mobileLayout ){
		event.setLayout('Layout.Mobile');
	}
}
```


