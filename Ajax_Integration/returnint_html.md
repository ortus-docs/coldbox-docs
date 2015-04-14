# Returnint HTML

HTML renderings is easy because that is what ColdBox does in its MVC core, render HTML. So to do a simple ajax call to the framework, you just call it as if you where doing it from the browser and requesting an event. The framework receives the request, process it, a view gets rendered and the ajax call receives the HTML snippet and you can div replace it anywhere you want.

```js
// From JavaScript, we just execute normal ColdBox events:

// Div loading of data
$("#myDiv").load('index.cfm/Security/sayHello');
$("#login").load('index.cfm/Security/loginForm');


// Get
$.get('index.cfm/Security/sayHello', function(data){
	$('#myDiv').html( data )
});

// Post
$.post('index.cfm/Security/sayHello', {name:'My Name', age=40}, function(data){
	$('#myDiv').html( data )
});
```

