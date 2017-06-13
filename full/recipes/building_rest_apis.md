# Building REST APIs

## Overview

REST APIs are a popular and easy way to add HTTP endpoints to your web applications to act as web services for third parties or even other internal systems. REST is simpler and requires less verbosity and overhead than other protocols such as SOAP or XML-RPC. 

Creating a fully-featured REST API is easy with the ColdBox Platform. Everything you need for creating routes, massaging data, and enforcing security comes out of the box. We even have an application template just for REST, so you can use CommandBox to create your first RESTFul app:

```bash
## create app
coldbox create app name=MyRestAPP skeleton=rest

## Start up a server with rewrites
server start
```

You will then see the following JSON output:

```js
{
    data: "Welcome to my ColdBox RESTFul Service",
    error: false,
    messages: [ ],
    errorcode: "0"
}
```

## Quick Reference Card
Below you can download our quick reference card on RESTFul APIs
 
[Download RefCard](https://github.com/ColdBox/cbox-refcards/raw/master/ColdBox%20REST%20APIs/ColdBox-REST-APIs-Refcard.pdf)


## Introduction To REST

REST stands for **Representational State Transfer** and builds upon the basic idea that data is represented as **resources** and accessed via a **URI**, or unique address. An HTTP client (such as a browser, or the `CFHTTP` tag) can send requests to a URI to interact with it. The HTTP verb (GET, POST, etc) sent in the header of the request tells the server how the client want to interact with that resource.

As far as how your data is formatted or how you implement security is left up to you. REST is less prescriptive than other standards such as SOAP (which uses tons of heavy XML and strictly-typed parameters). This makes it more natural to understand and easier to test and debug.

To use REST in ColdBox ensure you have the SES interceptor enabled (Which is enabled by default for all applications). Make sure the following interceptor is defined in your `/config/ColdBox.cfc` configuration file.

```js
interceptors = [
  {class="coldbox.system.interceptors.SES"}
];
```

## Defining Resources

A REST API can define its resources on its own domain (`http://api.example.com`), or after a static placeholder that differentiates it from the rest of the app (`http://www.example.com/api/`). We'll use the latter for these examples. 

Let's consider a resource we need to represent called `user`. Resources should usually be nouns. If you have a verb in your URL, you're probably doing it wrong. 

> **Hint** It is also important to note that REST is a style of URL architecture not a mandate, so it is an open avenue of sorts. However, you must stay true to its concepts of resources and usage of the HTTP verbs. 


Here are a few pointers when using the HTTP verbs:

```
http://www.example.com/api/user
```

* `GET /api/user` will return a representation of all the users. It is permissible to use a query string to control pagination or filtering.
* `POST /api/user/` will create a new user
* `GET /api/user/53` will return a representation of user 53
* `PUT /api/user/53` will update user 53
* `DELETE /api/user/53` will delete user 53

> **Info** GET, PUT, and DELETE methods should be idempotent which means repeated requests to the same URI don't do anything. Repeated POST calls however, would create multiple users.

In ColdBox, the easiest way to represent our `/api/user` resource is to create a handler called `user.cfc` in the `/handlers/api/` directory. In this instance, ColdBox will consider the `api` to be a handler package. You can leverage CommandBox for this:

```bash
coldbox create handler name=api.user actions=index,view,save,remove
```

> **Hint** This command will create all the necessary files for you and even the integration tests for you.

Here in my handler, I have stubbed out actions for each of the operations I need to perform against my user resource.

**/handlers/api/user.cfc**
```js
component {

  function index( event, rc, prc ) {
    // List all users
  }

  function view( event, rc, prc ) {
    // View a single user
  }

  function save( event, rc, prc ) {
    // Save a user
  }

  function remove( event, rc, prc ) {
    // Remove a user
  }

}
```

##Defining URL Routes

Now that we have this skeleton in place to represent our user resource, let's move on show how you can have full control of the URL as well as mapping HTTP verbs to specific handler actions. 

The default route for our `user.cfc` handler is `/api/user`, but what if we want the resource in the URL to be completely different than the handler name convention? To do this, use the `/config/routes.cfm` file to declare URL routes we want the application to capture and define how to process them.

Let's add the following new routes to our `/config/routes.cfm` file BEFORE the default route.

```js
// map base route to list users
addRoute(
  pattern = 'api/user',
  handler = 'api.user',
  action = 'index'
);

// Map route to specific user.  Different verbs call different actions!
addRoute(
  pattern = 'api/user/:userID?',
  handler = 'api.user',
  action = {
    GET = 'view',
    POST = 'save',
    PUT = 'save',
    DELETE = 'remove'
  });
```

You can see if that if action is a string, all HTTP verbs will be mapped there, however a `struct` can also be provided that maps different verbs to different actions. This gives you exact control over how the requests are routed.

### Route Placeholders

The `:userID` part of the route pattern is a placeholder. It matches whatever text is in the URL in that position. The value of the text that is matched will be available to you in the request collection as `rc.userID`. You can get even more specific about what kind of text you want to match in your route pattern.

#### Numeric Pattern Matcher

Append `-numeric` to the end of the placeholder to only match numbers.

`addRoute( pattern = 'user/:userID-numeric' );`

This route will match `user/123` but not `user/bob`.

#### Alphabetic Pattern Matcher

Append `-alpha` to the end of the placeholder to only match upper and lowercase letters.

`addRoute( pattern = 'page/:slug-alpha' );`

This route will match `page/contactus` but not `page/contact-us3`.

#### Regex Pattern Matcher

For full control, you can specify your own regex pattern to match parts of the route

`addRoute( pattern = 'api/regex:(user|person)' );`

This route will match `api/user` and `api/person`, but not `/api/contact`

#### Placeholder Quantifiers

You can also add the common regex `{}` quantifier to restrict how many digits a placeholder should have or be between:

```js
// two digits
:state{2}

// 2-4 digits
:year{2,4}

// 2 or more
:page{2,}

```

> **Hint** If a route is not matched it will be skipped and the next route will be inspected. If you want to validated parameters and return custom error messages inside your handler, then don't put the validations on the route.

As you can see, you have many options to craft the URL routes your API will use. Routes can be as long as you need. You can even nest levels for URLs like `/api/users/contact/address/27` which would refer to the address resource inside the contact belonging to a user.

## Returning Representations (Data)
REST does not dictate the format of data you use to represent your data. It can be JSON, XML, WDDX, plain text, or something else of your choosing.

### renderData()

The most common way to return data from your handler's action is to use the Request Context `renderData()` method. It takes complex data and turns it into a string representation. Here are some of the most common formats supported by `event.renderData()`:
* XML
* JSON
* TEXT
* WDDX
* PDF
* Custom

```js
// xml marshalling
function getUsersXML( event, rc, prc ){
	var qUsers = getUserService().getUsers();
	event.renderData( type="XML", data=qUsers );
}
//json marshalling
function getUsersJSON( event, rc, prc ){
	var qUsers = getUserService().getUsers();
	event.renderData( type="json", data=qUsers );
}
// pdf marshalling
function getUsersAsPDF( event, rc, prc ){
    prc.user = userService.getUser( rc.id );
    event.renderData( data=renderView( "users/pdf" ), type="pdf" );
}
// Multiple formats
function listUsers( event, rc, prc ){
    prc.users = userService.getAll();
    event.renderData( data=prc.users, formats="json,xml,html,pdf" );
}
```


### Format Detection
Many APIs allow the user to choose the format they want back from the endpoint.  ColdBox will inspect the `Accepts` header to determine the right format to use by default.

Another way to do this is by appending a file `extension` to the end of the URL:

```
http://www.example.com/api/user.json
http://www.example.com/api/user.xml
http://www.example.com/api/user.text
```

ColdBox has built-in support for detecting an extension in the URL and will save it into the request collection in a variable called `format`. What's even better is that `renderData()` can find the format variable and automatically render your data in the appropriate way. All you need to do is pass in a list of valid rendering formats and `renderData()` will do the rest.

```js
function index( event, rc, prc ) {
var qUsers = getUserService().getUsers();
// Correct format auto-detected from the URL
event.renderData( data=qUsers, formats="json,xml,text" );
}
```

### Handler Return Data

You can also return a string representation of the data directly from the method if you want to manually create it. It is important that you return a string. Complex data can't be sent over HTTP.

In this example, there are no views or layouts in play-- just raw data being returned from our handler.

```js
function index( event, rc, prc ) {
return 'This is the result of my REST call';
}
```

> **Info** This approach allows the user to render back any string representation and be able to output any content type they like.

### Status Codes

Status codes are a core concept in HTTP and REST APIs use them to send messages back to the client. Here are a few sample REST status codes and messages.

* `200` - OK - Everything is hunky-dory
* `202` - Created - The resource was created successfully
* `400` - Bad Request - The server couldn't figure out what the client was sending it
* `401` - Unauthorized - The client isn't authorized to access this resource
* `404` - Not Found - The resource was not found
* `500` - Server Error - Something bad happened on the server

You can easily set status codes as well as the status message with `renderData()`. HTTP status codes and messages are not part of the response body. They live in the HTTP header.

```js
function view( event, rc, prc ){
	var qUser = getUserService().getUser( rc.userID );
	if( qUser.recordCount ) {
		event.renderData( type="JSON", data=qUser );
	} else {
		event.renderData( type="JSON", data={}, statusCode=404, statusMessage="User not found");
	}
}

function save( event, rc, prc ){
	// Save the user represented in the request body
	var userIDNew = userService.saveUser( event.getHTTPContent() );
	// Return back the new userID to the client
	event.renderData( type="JSON", data={ userID = userIDNew }, statusCode=201, statusMessage="We have created your user");
}
```

Status codes can also be set manually by using the `event.setHTTPHeader()`method in your handler.


```js
function worldPeace( event, rc, prc ){
	event.setHTTPHeader( statusCode=501, statusText='Not Implemented' );
	return 'Try back later.';
}
```

### Caching

One of the great benefits of building your REST API on the ColdBox platform is tapping into great features such as event caching. Event caching allows you to cache the entire response for a resource using the incoming FORM and URL variables as the cache key. To enable event caching, set the following flag to true in your ColdBox config: `Coldbox.cfc`:

```
coldbox.eventCaching = true;
```

Next, simply add the `cache=true` annotation to any action you want to be cached. That's it! You can also get fancy, and specify an optional `cacheTimeout` and `cacheLastAccesstimeout` (in minutes) to control how long to cache the data.

```
// Cache for default timeout
function showEntry( event, rc, prc ) cache="true" {
	prc.qEntry = getEntryService().getEntry( event.getValue( 'entryID', 0 ) );		
	event.renderData( type="JSON", data=prc.qEntry );
}

// Cache for one hour, or 20 minutes from the last time accessed.
function showEntry( event, rc, prc ) cache="true" cacheTimeout="60" cacheLastAccessTimeout="20" {
	prc.qEntry = getEntryService().getEntry( event.getValue( 'entryID', 0 ) );		
	event.renderData( type="JSON", data=prc.qEntry );
}
```

Data is stored in CacheBox's `template` cache. You can configure this cache to store its contents anywhere including a Couchbase cluster!

## Security

Adding authentication to an API is a common task and while there is no standard for REST, ColdBox supports just about anything you might need.

### Requiring SSL
To prevent man-in-the-middle attacks or HTTP sniffing, we recommend your API require SSL. (This assumes you have purchased an SSL Cert and installed it on your server). When you define you routes, you can add `SSL=true` and ColdBox will only allow those routes to be access securely

```js
// Secure Route
addRoute(
  pattern = 'api/user',
  handler = 'api.user',
  action = 'index',
  SSL = true
);
````

If your client is capable of handling cookies (like a web browser) you can use the session or client scopes to store login details. Generally speaking, your REST API should be stateless, meaning nothing is stored on the server after the request completes. In this scenario, authentication information is passed along with every request. It can be passed in HTTP headers or as part of the request body. How you do this is up to you.

### Basic HTTP Auth

One of the simplest and easiest forms of authentication is Basic HTTP Auth. Note, this is not the most robust or secure method of authentication and most major APIs such as Twitter and FaceBook have all moved away from it. In Basic HTTP Auth, the client sends a header called Authorization that contains a base 64 encoded concatenation of the username and password.

You can easily get the username and password using `event.getHTTPBasicCredentials()`.

```js
function preHandler( event, action, eventArguments ){
	var authDetails = event.getHTTPBasicCredentials();
	if( !securityService.authenticate( authDetails.username, authDetails.password ) ) {
		event.renderData( type="JSON", data={ message = 'Please check your credentials' }, statusCode=401, statusMessage="You're not authorized to do that");
	}
}
```

### Custom

The previous example put the security check in a `preHandler()` method which will get automatically run prior to each action in that handler. You can implement a broader solution by tapping into any of the ColdBox interception points such as `preProcess` which is announced at the start of every request. Remember interceptors can include an `eventPattern` annotation to limit what ColdBox events they apply to.

In addition to having access to the entire request collection, the event object also has handy methods such as `event.getHTTPHeader()`` to pull specific headers from the HTTP request.

**/interceptors/APISecurity.cfc**
```js
/**
* This interceptor secures all API requests
*/
component{
	// This will only run when the event starts with "api."
	function preProcess( event, interceptData, buffer ) eventPattern = '^api\.' {
		var APIUser = event.getHTTPHeader( 'APIUser', 'default' );
		
		// Only Honest Abe can access our API
		if( APIUser != 'Honest Abe' ) {
			// Every one else will get the error response from this event
			event.overrideEvent('api.general.authFailed');
		}
	}
}
```

Register the interceptor with ColdBox in your `ColdBox.cfc`:

```
interceptors = [
  {class="coldbox.system.interceptors.SES"},
  {class="interceptors.APISecurity"}
];
```

As you can see there are many points to apply security to your API. One not covered here would be to tap into WireBox's AOP and place your security checks into an advice that can be bound to whatever API method you need to be secured.

### Restricting HTTP Verbs
In our route configuration we mapped HTTP verbs to handlers and actions, but what if users try to access resources directly with an invalid HTTP verb? You can easily enforce valid verbs (methods) by adding `this.allowedMethods` at the top of your handler. In this handler the `list()` method can only be access via a GET, and the `remove()` method can only be access via POST and DELETE.

```js
component{
	
	this.allowedMethods = { 
		remove = "POST,DELETE",
		list   = "GET"
	};

	function list( event, rc, prc ){}

	function remove( event, rc, prc ){}
}
```

The key is the name of the action and the value is a list of allowed HTTP methods. If the action is not listed in the structure, then it means allow all. If the request action HTTP method is not found in the list then it throws a 405 exception. 
You can catch this scenario and still return a properly-formatted response to your clients by using the `onError()` or the `onInvalidHTTPMethod()` convention in your handler or an exception handler which applies to the entire app.

## Error Handling
ColdBox REST APIs can use all the same error faculties that an ordinary ColdBox application has. We'll cover three of the most common ways here.

### Handler `onError()`
If you create a method called `onError()` in a handler, ColdBox will automatically call that method for runtime errors that occur while executing any of the actions in that handler. This allows for localized error handling that is customized to that resource.

```js
// error uniformity for resources
function onError( event, rc, prc, faultaction, exception ){
	prc.response = getModel("ResponseObject");
	
	// setup error response
	prc.response.setError(true);
	prc.response.addMessage("Error executing resource #arguments.exception.message#");
	
	// log exception
	log.error( "The action: #arguments.faultaction# failed when requesting resource: #arguments.event.getCurrentRoutedURL()#", getHTTPRequestData() );
	
	// display
	arguments.event.setHTTPHeader(statusCode="500",statusText="Error executing resource #arguments.exception.message#")
		.renderData( data=prc.response.getDataPacket(), type="json" );
}
```

### Handler `onInvalidHTTPMethod()`
If you create a method called `onInvalidHTTPMethod()` in a handler, ColdBox will automatically call that method whenever an action is trying to be executed with an invalid HTTP verb. This allows for localized error handling that is customized to that resource.

```js
/**
* on invalid http verbs
*/
function onInvalidHTTPMethod( event, rc, prc, faultAction, eventArguments ){
	// Log Locally
	log.warn( "InvalidHTTPMethod Execution of (#arguments.faultAction#): #event.getHTTPMethod()#", getHTTPRequestData() );
	// Setup Response
	prc.response = getModel( "Response" )
		.setError( true )
		.setErrorCode( 405 )
		.addMessage( "InvalidHTTPMethod Execution of (#arguments.faultAction#): #event.getHTTPMethod()#" )
		.setStatusCode( 405 )
		.setStatusText( "Invalid HTTP Method" );
	// Render Error Out
	event.renderData( 
		type		= prc.response.getFormat(),
		data 		= prc.response.getDataPacket(),
		contentType = prc.response.getContentType(),
		statusCode 	= prc.response.getStatusCode(),
		statusText 	= prc.response.getStatusText(),
		location 	= prc.response.getLocation(),
		isBinary 	= prc.response.getBinary()
	);
}
```

### Global Exception Handler

The global exception handler will get called for any runtime errors that happen anywhere in the typical flow of your application. This is like the `onError()` convention but covers the entire application. First, configure the event you want called in the `ColdBox.cfc` config file. The event must have the handler plus action that you want called.

```
coldbox = {
	...
	exceptionHandler = "main.onException"
	...
};
```

Then create that action and put your exception handling code inside. You can choose to do error logging, notifications, or custom output here. You can even run other events.

```js
// /handlers/main.cfc
component {
	function onException( event, rc, prc ){
		// Log the exception via LogBox
		log.error( prc.exception.getMessage() & prc.exception.getDetail(), prc.exception.getMemento() );
	
		// Flash where the exception occurred
		flash.put("exceptionURL", event.getCurrentRoutedURL() );
	
		// Relocate to fail page
		setNextEvent("main.fail");
	}
}
```

## ColdBox Relax

![](/images/relax.png)

ColdBox Relax is a set of ReSTful Tools For Lazy Experts. We pride ourselves in helping you (the developer) work smarter and ColdBox Relax is a tool to help you document your projects faster. ColdBox Relax provides you with the necessary tools to automagically model, document and test your ReSTful services. One can think of ColdBox Relax as a way to describe ReSTful web services, test ReSTful web services, monitor ReSTful web services and document ReSTful web services–all while you relax!

```
install relax --saveDev
```

**Please note:** Installing relax via CommandBox installs without the examples, if required you will need to obtain the examples from the Relax Github repo here: https://github.com/ColdBox/coldbox-relax 

To install the examples place them into the models directory in a subdirectory called 'resources' (as per the Github repo), then add the following `relax` structure to your `Coldbox.cfc` file:

```
relax = {
    // The location of the relaxed APIs, defaults to models.resources
    APILocation = "models.resources",
    // Default API to load, name of the directory inside of resources
    defaultAPI = "myapi",
    // History stack size, the number of history items to track in the RelaxURL
    maxHistory = 10
};
```

You can then visit `http://[yourhost]/relax` to view Relax.

You can read more about Relax on the Official Relax Doc page (http://www.ortussolutions.com/products/relax) or at it's repository: https://github.com/coldbox/coldbox-relax



----
<small>
Ref Card - https://github.com/ColdBox/cbox-refcards/raw/master/ColdBox%20REST%20APIs/ColdBox-REST-APIs-Refcard.pdf
</small>
