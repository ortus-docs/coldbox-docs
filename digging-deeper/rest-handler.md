---
description: ColdBox supports native REST handling via the RestHandler and native routing.
---

# REST Handler

## RestHandler & ColdBox Response

ColdBox 6 has native REST capabilities alongside the `RestHandler` and a customizable `Response` object into the core.  This `RestHandler` will provide you with utilities and approaches to make all of your RESTFul services:

* Uniformity on Responses
* A consistent and extensible response object
* Consistent Error handling
* Invalid route handling
* Internal Events
* Much more

{% hint style="info" %}
In previous versions of ColdBox support for RESTful services was provided by adding the base handler and the response object to our application templates. Integration into the core brings ease of use, faster handling and above all: a uniform approach for each ColdBox version in the future.
{% endhint %}

![RestHandler UML](../.gitbook/assets/resthandler.png)

## Base Class: RestHandler

For the creation of REST handlers you can inherit from our base class `coldbox.system.RestHandler`, directly via `extends="coldbox.system.Resthandler"` or by using the `restHandler` annotation.&#x20;

{% embed url="https://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox%2Fsystem%2FRestHandler.html" %}
RestHandler API Docs
{% endembed %}

This will give you access to our enhanced API of utilities and the native **response** object via the event object `getResponse()` method.

```javascript
component extends="coldbox.system.RestHandler"{

  function index( event, rc, prc ){
    event.getResponse()
      .setData( "Hello from restful Land" );
  }
}

component resthandler{

  function index( event, rc, prc ){
    event.getResponse()
      .setData( "Hello from restful Land" );
  }

}
```

You will then leverage that response object ([https://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/Response.html](https://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/Response.html)) to do the following actions:

* Set the data to be converted to JSON, XML or whatever
* Set pagination data
* Set errors
* Set if the data is binary or not
* Set `location` headers
* Set message arrays
* Set `json` call backs
* Set `json` query formats
* Set response `contentType`
* Set response `statusCode`
* Set response `headers`
* Much More

{% embed url="https://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox%2Fsystem%2Fweb%2Fcontext%2FResponse.html=" %}
Response API Docs
{% endembed %}

{% hint style="success" %}
**Upgrade Pointers:** The response object will be accessible using the `event.getResponse()` method but will still be available as `prc.response.`
{% endhint %}

{% hint style="info" %}
If you are using `cbORM` make sure to check the chapter [Automatic Rest Crud](https://coldbox-orm.ortusbooks.com/orm-events/automatic-rest-crud)
{% endhint %}

### Base Rest Handler Actions

The Rest handler gives you the following **actions** coded for you out of the box.  You can override them if you want, but the idea is that this base takes you a very long way.

| **Core Actions**                               | **Purpose**                                                                                                                                                                                                                                                                                                                        |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `aroundHandler()`                              | Wraps all rest actions uniformly to provide consistency and error trapping.                                                                                                                                                                                                                                                        |
| `onError()`                                    | An implicit error handler is provided just in case anything explodes in your restful actions. Sends an appropriate 500 error                                                                                                                                                                                                       |
| `onValidationException()`                      | Traps any and makes sure it sends the appropriate 400 response with the invalid data. Useful for using **cbValidation**, for example with the `validateOrFail()` methods.                                                                                                                                                          |
| `onEntityNotFoundException()`                  | Traps any or exceptions and makes sure it send an appropriate 404 response. Useful for leveraging **cborm** or **Quick** ORM                                                                                                                                                                                                       |
| `onInvalidHTTPMethod()`                        | Traps any invalid HTTP method security exception and sends the appropriate 405 not allowed response                                                                                                                                                                                                                                |
| `onMissingAction()`                            | Traps any invalid actions/resource called in your application and sends the appropriate 404 response                                                                                                                                                                                                                               |
| `onAuthenticationFailure()`                    | Traps `InvalidCredentials` exceptions and sends the appropriate 403 invalid credentials response. If you are using **cbSecurity** it will also verify jwt token expiration and change the error messages accordingly.                                                                                                              |
| `onAuthorizationFailure()`                     | Traps `PermissionDenied` exceptions and sends the appropriate 401 response.  This Action can be used when a user does not have authorization or access to your application or code. Usually you will call this manually or from a security library like **cbSecurity** or **cbGuard**. It will send a 401 not authorized response. |
| `onInvalidRoute()`                             | Action that can be used as a catch all from your router so it can catch all routes that are invalid. It will send a 404 response accordingly.                                                                                                                                                                                      |
| `onExpectationFailed()`                        | Utility method for when an expectation of the request fails ( e.g. an expected parameter is not provided ). This action is called manually from your own handlers and it will output a 417 response back to the user.                                                                                                              |
| <p></p><p><code>onAnyOtherException</code></p> | Fires when ANY exception that is not excplicitly trapped is detected.  This basically logs the issue and offers a 500 error.  You can now intercept it and do whatever you need on ANY type of untrapped exception.                                                                                                                |

### AroundHandler in Detail

The `aroundHandler`() provided in the `RestHandler` will intercept all rest calls in order to provide consistency and uniformity to all your actions. It will try/catch for major known exceptions, time your requests, add extra output on development and much more. Here are a list of the features available to you:

#### Exception Handling

Automatic trapping of the following exceptions:&#x20;

* `EntityNotFound`
* `InvalidCredentials`
* `PermissionDenied`
* `RecordNotFound`
* `TokenInvalidException`
* `ValidationException`

If the trapped exception is not one from above, then we will call the  `onAnyOtherException()` action.  This action will log automatically the exception with extra restful metadata according to the environment you are executing the action with.  It will also setup an exception response for you.

{% hint style="info" %}
If in a `development` environment it will respond with much more information necessary for debugging both in the response object and headers
{% endhint %}

#### Development Responses

If you are in a `development` environment it will set the following headers for you in the response object automatically.

* `x-current-event`
* `x-current-route`
* `x-current-routed-url`
* `x-current-routed-namespace`

#### Global Headers

The following headers are sent in each request no matter the environment:

* `x-response-time` : The time the request took in CF
* `x-cached-response` : If the request is cached via event caching

#### Output Detection

The `aroundHandler()` is also smart in detecting the following outputs from a handler, which will ignore the REST response object:

* Handler `return` results
* Setting a view or layout to render
* Explicit `renderData()` calls

### Rest Handler Security

The `RestHandler` also gives you HTTP method security out of the box by convention based on [ColdBox resources](../the-basics/routing/routing-dsl/resourceful-routes.md).  The following is already created for you using the `this.allowedMethods` [structure](../the-basics/event-handlers/http-method-security.md):

```javascript
// Default REST Security for ColdBox Resources
this.allowedMethods = {
	"index"  : "GET",
	"new"    : "GET",
	"get"    : "GET",
	"create" : "POST",
	"show"   : "GET",
	"list"   : "GET",
	"edit"   : "GET",
	"update" : "POST,PUT,PATCH",
	"delete" : "DELETE"
};
```

Also note that since this is a base class, you can override this structure or append to it as you see fit.

## Response Object

The `Response` object is used by the developer to set into it what the response will be by the API call.  The object is located at `coldbox.system.web.context.Response` and can be retrieved by the request context object's `getResponse()` method.

{% embed url="https://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox%2Fsystem%2Fweb%2Fcontext%2FResponse.html=" %}
Response API Docs
{% endembed %}

### Response Format

Every response is created from the internal properties in the following JSON representation:

```javascript
{
  "error"      : getError() ? true : false,
  "messages"   : getMessages(),
  "data"       : getData(),
  "pagination" : getPagination()
};
```

{% hint style="success" %}
You can change this response by extending the response object and doing whatever you want.
{% endhint %}

### Properties

The `Response` object has the following properties you can use via their getter and setter methods.

| Property        | Type      | Default                                                                                                                                                                  | Usage                                                                                                                                            |
| --------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| binary          | `boolean` | false                                                                                                                                                                    | If the `data` property is binary                                                                                                                 |
| contentType     | `string`  | ---                                                                                                                                                                      | Custom content type of the response                                                                                                              |
| data            | `struct`  | ---                                                                                                                                                                      | The data struct marshalled in the response                                                                                                       |
| error           | `boolean` | false                                                                                                                                                                    | Boolean bit denoting an exception or problem in the API call                                                                                     |
| format          | `string`  | `json`                                                                                                                                                                   | The response format of the API. Defaults to `json`.                                                                                              |
| jsonCallback    | `string`  | ---                                                                                                                                                                      | If using a callback then set the callback method here.                                                                                           |
| jsonQueryFormat | `string`  | `true`                                                                                                                                                                   | This parameter can be a Boolean value that specifies how to serialize ColdFusion queries or a string with possible values row, column, or struct |
| location        | `string`  | ---                                                                                                                                                                      | The location header to send with the response                                                                                                    |
|                 |           |                                                                                                                                                                          |                                                                                                                                                  |
| messages        | `array`   | ---                                                                                                                                                                      | Array of messages to send in the response packet                                                                                                 |
| pagination      | `struct`  | <p><code>{</code> <br><code>offset,</code><br><code>maxRows,</code><br><code>page,</code><br><code>totalRecords,</code><br><code>totalPages</code><br><code>}</code></p> | A pagination struct to return                                                                                                                    |
| responseTime    | `numeric` | 0                                                                                                                                                                        | The time the request and response took                                                                                                           |
| statusCode      | `numeric` | 200                                                                                                                                                                      | The status code to send                                                                                                                          |
| statusText      | `string`  | `OK`                                                                                                                                                                     | The status text to send                                                                                                                          |

### Convenience Methods

A part from the setters/getters from the properties above, the response object has some cool convenience methods:

```javascript
/**
 * Utility function to get the state of this object
 */
struct function getMemento()

/**
 * Add some messages to the response
 *
 * @message Array or string of message to incorporate
 */
Response function addMessage( required any message )

/**
 * Get all messages as a string
 */
string function getMessagesString()

/**
 * Add a header into the response
 *
 * @name  The header name ( e.g. "Content-Type" )
 * @value The header value ( e.g. "application/json" )
 */
Response function addHeader( required string name, required string value )

/**
 * Set the pagination data
 *
 * @offset       The offset
 * @maxRows      The max rows returned
 * @page         The page number
 * @totalRecords The total records found
 * @totalPages   The total pages found
 */
Response function setPagination(
	numeric offset       = 0,
	numeric maxRows      = 0,
	numeric page         = 1,
	numeric totalRecords = 0,
	numeric totalPages   = 1
)

/**
 * Returns a standard response formatted data packet using the information in the response
 *
 * @reset Reset the 'data' element of the original data packet
 */
struct function getDataPacket( boolean reset = false )

/**
 * Sets the status code with a statusText for the API response
 *
 * @code The status code to be set
 * @text The status text to be set
 *
 * @return Returns the Response object for chaining
 */
Response function setStatus( required code, text )

/**
 * Sets the data and pagination from a struct with `results` and `pagination`.
 *
 * @data          The struct containing both results and pagination.
 * @resultsKey    The name of the key with the results.
 * @paginationKey The name of the key with the pagination.
 *
 * @return Returns the Response object for chaining
 */
Response function setDataWithPagination(
	data,
	resultsKey    = "results",
	paginationKey = "pagination"
)

/**
 * Sets the error message with a code for the API response
 *
 * @errorMessage The error message to set
 * @statusCode   The status code to set, if any
 * @statusText   The status text to set, if any
 *
 * @return Returns the Response object for chaining
 */
Response function setErrorMessage(
	required errorMessage,
	statusCode,
	statusText = ""
)}
```

### Status Text Lookup

The response object also has as `STATUS_TEXTS` public struct exposed so you can use it for easy status text lookups:

```javascript
this.STATUS_TEXTS = {
    "100" : "Continue",
    "101" : "Switching Protocols",
    "102" : "Processing",
    "200" : "OK",
    "201" : "Created",
    "202" : "Accepted",
    "203" : "Non-authoritative Information",
    "204" : "No Content",
    "205" : "Reset Content",
    "206" : "Partial Content",
    "207" : "Multi-Status",
    "208" : "Already Reported",
    "226" : "IM Used",
    "300" : "Multiple Choices",
    "301" : "Moved Permanently",
    "302" : "Found",
    "303" : "See Other",
    "304" : "Not Modified",
    "305" : "Use Proxy",
    "307" : "Temporary Redirect",
    "308" : "Permanent Redirect",
    "400" : "Bad Request",
    "401" : "Unauthorized",
    "402" : "Payment Required",
    "403" : "Forbidden",
    "404" : "Not Found",
    "405" : "Method Not Allowed",
    "406" : "Not Acceptable",
    "407" : "Proxy Authentication Required",
    "408" : "Request Timeout",
    "409" : "Conflict",
    "410" : "Gone",
    "411" : "Length Required",
    "412" : "Precondition Failed",
    "413" : "Payload Too Large",
    "414" : "Request-URI Too Long",
    "415" : "Unsupported Media Type",
    "416" : "Requested Range Not Satisfiable",
    "417" : "Expectation Failed",
    "418" : "I'm a teapot",
    "421" : "Misdirected Request",
    "422" : "Unprocessable Entity",
    "423" : "Locked",
    "424" : "Failed Dependency",
    "426" : "Upgrade Required",
    "428" : "Precondition Required",
    "429" : "Too Many Requests",
    "431" : "Request Header Fields Too Large",
    "444" : "Connection Closed Without Response",
    "451" : "Unavailable For Legal Reasons",
    "499" : "Client Closed Request",
    "500" : "Internal Server Error",
    "501" : "Not Implemented",
    "502" : "Bad Gateway",
    "503" : "Service Unavailable",
    "504" : "Gateway Timeout",
    "505" : "HTTP Version Not Supported",
    "506" : "Variant Also Negotiates",
    "507" : "Insufficient Storage",
    "508" : "Loop Detected",
    "510" : "Not Extended",
    "511" : "Network Authentication Required",
    "599" : "Network Connect Timeout Error"
};
```

## Extending The **RestHandler**

If you would like to extend or modify the behavior of the core `RestHandler` then you will have to create your own base handler that inherits from it. Then all of your concrete handlers will inherit from your very own handler.

```javascript
// BaseHandler
component extends="coldbox.system.Resthandler"{

  // Modify it here

}

// Then make your own handlers extend from it
component extends="BaseHandler"{

}
```

## Extending The **Response** Object

The response object can be found here: `coldbox.system.web.context.Response` and the rest handler constructs it by calling the request context’s `getResponse`() method. The method verifies if there is a `prc.response` object and if it exists it returns it, else it creates a new one. So if you would like to use your very own, then just make sure that before the request you place your own response object in the `prc` scope.

Here is a simple example using a `preProcess()` interceptor. Create a simple interceptor with commandbox e.g

```javascript
coldbox create interceptor name=MyInterceptor points=preProcess
```

and add the following method:

```javascript
function preProcess( event, interceptData, rc, prc ){
  prc.response = wirebox.getInstance( "MyResponseObject" );
}
```

Don't forget to register your interceptor in `config/Coldbox.cfc:`

```javascript
        interceptors = [
            {
            class      : "interceptors.MyInterceptor",
                name       : "MyInterceptor",
                properties : {}
            }
        ];
```

That’s it. Once that response object is in the `prc` scope, ColdBox will utilize it. Just make sure that your custom Response object satisfies the methods in the core one. If you want to modify the output of the response object a good place to do that would be in the `getDataPacket()` method of your own `MyResponseObject`. Just make sure this method will return a `struct`.
