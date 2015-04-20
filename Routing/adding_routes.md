# Adding Routes


```js
public any addRoute(string pattern, [string handler], [any action], [boolean packageResolverExempt='false'], [string matchVariables], [string view], [boolean viewNoLayout='false'], [boolean valuePairTranslation='true'], [any constraints=''], [string module=''], [string moduleRouting=''], [string namespace=''], [string namespaceRouting=''], [boolean ssl='false'], [boolean append='true'])
```

|Argument|Type|Required|Default|Description|
|--|--|--|--|--|
|pattern|string|true|---|The URL pattern to look for in the incoming URL|
|handler |string|false|---|The handler to translate to, can include module or package path|
|action|string or struct|false|---|The action to translate to. If a structure, then the keys represent the HTTP verbs of the RESTful action to relocate to.|
|packageResolverExempt |boolean|false|true|Automatically can resolve packages in the URL when using routing by convention|
|matchVariables |string|false|---|A query string of variables to inject into the request collection if the route matches|
|view|string|false|---|The name of the view to dispatch the URL to instead of event routing|
|viewNoLayout |boolean|false|false|Use no layout when rendering the view dispatched or not|
|valuePairTranslation |boolean|false|true|By default it converts any name-value pair after the matched URL pattern to the request collection|
|constraints |struct|false|{}|A structure of regex constraints for variable placeholders in the URL patterns|
|module|string|false|---|Add this route to a named module|
|moduleRouting |boolean|false|false|Called internally by addModuleRoutes to add a module routing route only.|
|namespace |string|false|---|The namespace to add this route to|
|ssl|boolean|false|false|Makes the route SSL only if true, else for either SSL or non SSL. If SSL, the interceptor will relocate to the same route but in SSL|
|append |boolean|false|true|By default all routes are stored in first come first served order, but you can pre-pend to the first position if so desired.|
|response |string or closure or UDF |false|---|A simple string to return if the route matched or a closure to execute that must return a string to return back to the user. No ColdBox events will be fired automatically and response will be sent immediately|
|statusCode |numeric|false|200|The status code header to send with your response if using the response arguments|
|statusText |string|false|---|The status text header to send with your response if using the response arguments|
|condition|closure or UDF|false|---|A closure or UDF that MUST return *boolean* as a secondary check on the pattern matching and receives the matched *requeststring* as a parameter. Great for not only doing pattern matching but also CUSTOM conditions.|

This is the meat and potatoes for enabling routing. You use this method to declare routes that will be dispatched by the interceptor. The syntax of these are similar to Ruby on Rails. The idea is that the number of variables in a URL will be the first indicator of which course to use. Basically, the interceptor goes through each rule in a top-down format and tries to match the incoming URL to the route. If a route matches it will try to create the appropriate event and extra variables in order to respond to a request.

> **Important** You can pass any named argument and value to this method and the interceptor will create a new variable with the name of the argument in the request collection for you. This can be used as an alternative to using the matchVariables argument. 