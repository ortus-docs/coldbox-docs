# HTTP Testing Methods

If you are building RESTFul services with ColdBox then you will also need a way to test them.  We have created the following methods to help you test ANY HTTP method, param and even headers.

## request\(\)

The `request()` method is the method to use to simulate ANY HTTP verb operation and get a response rendered into the request context.  Please note that it expects a `route` and not an event.  This means that it will also test your URL mappings, so make sure you pass the right URL.

```javascript
/**
 * Shortcut method to making a request through the framework.
 *
 * @route The route to execute.
 * @params Params to pass to the `rc` scope.
 * @headers Custom headers to pass as from the request
 * @method The method type to execute.  Defaults to GET.
 * @renderResults If true, then it will try to do the normal rendering procedures and store the rendered content in the RC as cbox_rendered_content
 * @withExceptionHandling If true, then ColdBox will process any errors through the exception handling framework instead of just throwing the error. Default: false.
 */
function request(
	string route                  = "",
	struct params                 = {},
	struct headers                = {},
	string method                 = "GET",
	boolean renderResults         = true,
	boolean withExceptionHandling = false
)
```

## get\(\)

```javascript
/**
 * Shortcut method to making a GET request through the framework.
 *
 * @route The route to execute.
 * @params Params to pass to the `rc` scope.
 * @headers Custom headers to pass as from the request
 * @renderResults If true, then it will try to do the normal rendering procedures and store the rendered content in the RC as cbox_rendered_content
 * @withExceptionHandling If true, then ColdBox will process any errors through the exception handling framework instead of just throwing the error. Default: false.
 */
function get(
	string route                  = "",
	struct params                 = {},
	struct headers                = {},
	boolean renderResults         = true,
	boolean withExceptionHandling = false
)
```

## post\(\)

```javascript
/**
 * Shortcut method to making a POST request through the framework.
 *
 * @route The route to execute.
 * @params Params to pass to the `rc` scope.
 * @headers Custom headers to pass as from the request
 * @renderResults If true, then it will try to do the normal rendering procedures and store the rendered content in the RC as cbox_rendered_content
 * @withExceptionHandling If true, then ColdBox will process any errors through the exception handling framework instead of just throwing the error. Default: false.
 */
function post(
	string route                  = "",
	struct params                 = {},
	struct headers                = {},
	boolean renderResults         = true,
	boolean withExceptionHandling = false
)
```

## put\(\)

```javascript
/**
 * Shortcut method to making a PUT request through the framework.
 *
 * @route The route to execute.
 * @params Params to pass to the `rc` scope.
 * @headers Custom headers to pass as from the request
 * @renderResults If true, then it will try to do the normal rendering procedures and store the rendered content in the RC as cbox_rendered_content
 * @withExceptionHandling If true, then ColdBox will process any errors through the exception handling framework instead of just throwing the error. Default: false.
 */
function put(
	string route                  = "",
	struct params                 = {},
	struct headers                = {},
	boolean renderResults         = true,
	boolean withExceptionHandling = false
)
```

## patch\(\)

```javascript
/**
 * Shortcut method to making a PATCH request through the framework.
 *
 * @route The route to execute.
 * @params Params to pass to the `rc` scope.
 * @headers Custom headers to pass as from the request
 * @renderResults If true, then it will try to do the normal rendering procedures and store the rendered content in the RC as cbox_rendered_content
 * @withExceptionHandling If true, then ColdBox will process any errors through the exception handling framework instead of just throwing the error. Default: false.
 */
function patch(
	string route                  = "",
	struct params                 = {},
	struct headers                = {},
	boolean renderResults         = true,
	boolean withExceptionHandling = false
)
```

## delete\(\)

```javascript
/**
 * Shortcut method to making a DELETE request through the framework.
 *
 * @route The route to execute.
 * @params Params to pass to the `rc` scope.
 * @headers Custom headers to pass as from the request
 * @renderResults If true, then it will try to do the normal rendering procedures and store the rendered content in the RC as cbox_rendered_content
 * @withExceptionHandling If true, then ColdBox will process any errors through the exception handling framework instead of just throwing the error. Default: false.
 */
function delete(
	string route                  = "",
	struct params                 = {},
	struct headers                = {},
	boolean renderResults         = true,
	boolean withExceptionHandling = false
)
```

## Examples

```javascript
story( "I want to authenticate a user via username/password and receive a JWT token", function(){
		given( "a valid username and password", function(){
			then( "I will be authenticated and will receive the JWT token", function(){
				// Use a user in the seeded db
				var event = this.post(
					"/api/v1/login",
					{
						username : variables.testEmployeeEmail,
						password : variables.testPassword
					}
				);
				var response = event.getPrivateValue( "Response" );
				expect( response.getError() ).toBeFalse( response.getMessagesString() );
				expect( response.getData() ).toHaveKey( "token,user" );
				
				// debug( response.getData() );
				
				var decoded = jwt.decode( response.getData().token );
				expect( decoded.sub ).toBe( variables.testEmployeeId );
				expect( decoded.exp ).toBeGTE( dateAdd( "h", 1, decoded.iat ) );
				expect( response.getData().user.userId ).toBe( variables.testEmployeeId );
			} );
		} );
		
		given( "invalid username and password", function(){
			then( "I will receive a 401 invalid credentials exception ", function(){
				var event = this.post(
					"/api/v1/login",
					{ username : "invalid", password : "invalid" }
				);
				var response = event.getPrivateValue( "Response" );
				expect( response.getError() ).toBeTrue();
				expect( response.getStatusCode() ).toBe( 401 );
			} );
		} );
		
		given( "an invalid token", function(){
			then( "I should get an error", function(){
				// Now Logout
				var event = GET(
					"/api/v1/whoami",
					{ "x-auth-token" : "123" }
				);
				expect( event.getResponse() ).toHaveStatus( 500 );
			} );
		} );
		
	} );
```

