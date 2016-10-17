# Route Responses

You can route responses inline via `addRoute()` with parameters: `response, statusCode and statusText`. The `response` argument can be a string or a closure or UDF. The closure and UDF will receive a `rc` argument which represents the request collection parsed already for you. The simple string can also contain replacement strings that are binded to the parsed parameters from the pattern by using `{key}` replacements. You can even execute `runEvent()` within your closure and delegate to whatever event you want instead of auto-routing:


```js
// Simple response routing
addRoute(pattern="/users/hello", response="<h1>Hello From RESTLand</h1>", statusCode=200);

// Simple response routing with placeholders
addRoute(pattern="/users/:username", response="<h1>Hello {username} From RESTLand</h1>", statusCode=200);

// Closure/UDF response routing
addRoute(pattern="/users/:username", response=function(rc){
  // Do some code here
  // more code
  return "<h1> Hello #arguments.rc.username# from RESTLand</h1>";
});

// Closure/UDF response routing with event support
addRoute(pattern="/users/:username", response=function(rc){
  // Do some code here
  
  // more code
  return runEvent(event='api:users.data', eventArguments={quickData=true});
});
```

