# Route Conditions

The *addRoute()* has an argument called condition which can be a closure or UDF that MUST return boolean as a secondary check on the pattern matching and receives the matched requeststring as a parameter. So if a route matches via the pattern, then this closure/UDF is called to verify the route with your own custom conditions. This is a great way to create some routes that must match certain environment criterias before they fire. Let's say you only want to fire some routes if they are using FireFox, or a user is logged in, or whatever.

```js
// condition closure
function(requestString){}

// Routing with condition
addRoute(pattern="/go/ff", response="<h1>Hello FireFox!</h1>", condition=function(requestString){
  return ( findnocase( "Firefox", cgi.HTTP_USER_AGENT ) ? true : false );
});
```

