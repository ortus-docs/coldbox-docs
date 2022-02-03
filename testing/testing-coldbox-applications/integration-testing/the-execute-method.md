# The execute() Method

The `execute()` method is your way of making requests in to your ColdBox application. It can take the following parameters:

| Name                    | Type    | Default           | Description                                                                                                                          |
| ----------------------- | ------- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `event`                 | string  | ---               | The event to execute (e.g. 'main.index')                                                                                             |
| `route`                 | string  | ---               | The route to execute (e.g. '/login' which may route to 'sessions.new')                                                               |
| `queryString`           | string  | ---               | The query string to use in the request                                                                                               |
| `private`               | boolean | `false`           | If `true`, sets the event execution as private.                                                                                      |
| `prePostExempt`         | boolean | `false`           | If `true`, skips the pre- and post- interceptors. e.g., `preEvent`, `postHandler`, etc.                                              |
| `eventArguments`        | struct  | `{}`              | A collection of arguments to passthrough to the calling event handler method.                                                        |
| `renderResults`         | struct  | `false`           | If true, then it will try to do the normal rendering procedures and store the rendered content in the RC as `cbox_rendered_content`. |
| `withExceptionHandling` | boolean | `false`           | If true, then ColdBox will process any errors through the exception handling framework instead of just throwing the error.           |
| `domain`                | string  | `cgi.server_name` | The domain or subdomain that you would like to simulate the request with.                                                            |

This method will execute any ColdBox event/route just like if it's coming from the browser or mobile app.  You will get back a request context object from which you can then do assertions with it.

```javascript
it( "can render some restful data", function() {
    var event = execute( "main.data" );

    debug( event.getHandlerResults() );
    expect( event.getRenderedContent() ).toBeJSON();
} );
```

{% hint style="danger" %}
**WARNING:** Please note that this method is limited to simulating `GET` operations.  If you are building RESTFul services, then you will need to use our [HTTP Testing Methods](http-testing-methods.md) discussed next.
{% endhint %}

## Rendering Results

The `execute()` method has an argument called `renderResults` which defaults to **false**. If you pass in **true** then ColdBox will go through the normal rendering procedures and save the results in a request collection variable called: `cbox_rendered_content` and expose to you a method in the request context called `getRenderedContent()`. It will even work with `renderData()` or if you are returning RESTful information.&#x20;

Then you can easily assert what the content would have been for an event.

```javascript
it( "can render users", function(){

   var event = execute( event="rest.api.users", renderResults=true );
   
   // From Value
   expect( event.getValue( "cbox_rendered_content" ) ).toBeJSON();
   // From Method
   expect( event.getRenderedContent() ).toBeJSON();
   
} );
```

## Handler Returning Results

If you have written event handlers that actually return data, then you will have to get the values from the request collection to assert them. ColdBox will create a variable called: `cbox_handler_results` and expose to you a method in the request context called `getHandlerResults()` so you can do your assertions.

**The handler** (`main.cfc`)

```javascript
function list( event, rc, prc ){
    return "Hola Luis";
}
```

**The spec**

```javascript
it( "+homepage renders", function(){
    var event = execute( event="main.list", renderResults=true );
    
    // From value
    expect(	event.getValue( "cbox_handler_results" ) ).toBe( "Hola Luis" );
    // From Method
    expect( event.getHandlerResults() ).toBe( "Hola Luis" );
});
```

## Rendering Data

If you use the `renderData()` method in your handler, you can also get that rendering data struct via our convenience method: `getRenderData()` or via the request collection private variable: `cbox_renderdata`.

**The handler** (`main.cfc`)

```javascript
function data( event, rc, prc ){
    event.renderData( data=myService.getData(), type="json" );
}
```

**The spec**

```javascript
it( "can render data", function(){
    var event = execute( event="main.data", renderResults=true );
    
    // From value
    expect(	event.getPrivateValue( "cbox_renderdata" ).type ).toBe( "json" );
    // From Method
    expect( event.getRenderData().type ).toBe( "json" );
});
```

## Getting The Status Code

If you are expecting status codes to do expectations against, you can use our two convenience methods:

| Method                  | Description                                        |
| ----------------------- | -------------------------------------------------- |
| `getStatusCode()`       | Get the status code for a ColdBox integration test |
| `getNativeStatusCode()` | Get the status code set in the CFML engine.        |

You most likely will use the `getStatusCode()` as this is the one used in mocks and relocations, the other one is a RAW approach to get the underlying status code from the page context response.

## Mocking Relocations

ColdBox will wire itself up with some mocking classes to intercept those relocations for you and place those values in the request collection for you so you can assert them. It creates a key called `relocate` in the request collection and any arguments passed to the method are also saved as keys with the following pattern:

```
relocate_{argumentName}
```

**The possible arguments are:**

* `event`
* `URL`
* `URI`
* `queryString`
* `persist`
* `persistStruct`
* `addToken`
* `ssl`
* `baseURL`
* `postProcessExempt`
* `statusCode`

```javascript
it( "can do a relocation", function() {
    var event = execute( event = "main.doSomething" );
    expect( event.getValue( "relocate_event", "" ) ).toBe( "main.index" );
} );
```

## Subdomain/Domain Routing

If you are building multi-tenant applications with ColdBox and are leveraging [domain and subdomain routing](the-execute-method.md#undefined), then you can easily use the `domain` argument to simulate the domain in play for THAT specific spec execution.

```javascript

describe( "subdomain routing", function(){
	beforeEach( function(){
		setup();
	} );
	
	it( "can match on a specific domain", function(){
		var event = execute( route: "/", domain: "subdomain-routing.dev" );
		var rc    = event.getCollection();
		expect( rc ).toHaveKey( "event" );
		expect( rc.event ).toBe( "subdomain.index" );
	} );
	
	it( "skips if the domain is not matched", function(){
		var event = execute( route: "/", domain: "not-the-correct-domain.dev" );
		var rc    = event.getCollection();
		expect( rc ).toHaveKey( "event" );
		expect( rc.event ).toBe( "main.index" );
	} );
	
	it( "can match on a domain with wildcards", function(){
		var event = execute( route: "/", domain: "luis.forgebox.dev" );
		var rc    = event.getCollection();
		expect( rc ).toHaveKey( "event" );
		expect( rc.event ).toBe( "subdomain.show" );
	} );
	
	it( "provides any matched values in the domain in the rc", function(){
		var event = execute( route: "/", domain: "luis.forgebox.dev" );
		var rc    = event.getCollection();
		expect( rc ).toHaveKey( "username" );
		expect( rc.username ).toBe( "luis" );
	} );
} );

```
