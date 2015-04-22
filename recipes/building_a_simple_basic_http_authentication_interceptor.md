# Building a simple Basic HTTP Authentication Interceptor

### Introduction

In this recipe we will create a simple interceptor that will be in charge of challenging users with HTTP Basic Authentication. It features the usage of all the new RESTful methods in our Request Context that will make this interceptor really straightforward. We will start by knowing that this interceptor will need a security service to verify security, so we will also touch on this.

### The Interceptor
Our simple security interceptor will be intercepting at preProcess so all incoming requests are inspected for security. Remember that I will also be putting best practices in place and will be creating some unit tests and mocking for all classes. So check out our interceptor:


```js
/**
* Intercepts with HTTP Basic Authentication
*/
component {

	// Security Service
	property name="securityService" inject="id:SecurityService";


	void function configure(){
	}

	void function preProcess(event,struct interceptData){

		// Verify Incoming Headers to see if we are authorizing already or we are already Authorized
		if( !securityService.isLoggedIn() OR len( event.getHTTPHeader("Authorization","") ) ){

			// Verify incoming authorization
			var credentials = event.getHTTPBasicCredentials();
			if( securityService.authorize(credentials.username, credentials.password) ){
				// we are secured woot woot!
				return;
			};

			// Not secure!
			event.setHTTPHeader(name="WWW-Authenticate",value="basic realm=""Please enter your username and password for our Cool App!""");

			// secured content data and skip event execution
			event.renderData(data="<h1>Unathorized Access<p>Content Requires Authentication</p>",statusCode="401",statusText="Unauthorized")
				.noExecution();
		}

	}

}
```

As you can see it relies on a SecurityService model object that is being wired via:

```js
// Security Service
property name="securityService" inject="id:SecurityService";
```

Then we check if a user is logged in or not and if not we either verify their incoming HTTP basic credentials or if none, we challenge them by setting up some cool headers and bypass event execution:

```js
// Verify Incoming Headers to see if we are authorizing already or we are already Authorized
if( !securityService.isLoggedIn() OR len( event.getHTTPHeader("Authorization","") ) ){

	// Verify incoming authorization
	var credentials = event.getHTTPBasicCredentials();
	if( securityService.authorize(credentials.username, credentials.password) ){
		// we are secured woot woot!
		return;
	};

	// Not secure!
	event.setHTTPHeader(name="WWW-Authenticate",value="basic realm=""Please enter your username and password for our Cool App!""");

	// secured content data and skip event execution
	event.renderData(data="<h1>Unathorized Access<p>Content Requires Authentication</p>",statusCode="401",statusText="Unauthorized")
		.noExecution();
}
```

The renderData() is essential in not only setting the 401 status codes but also concatenating to a noExecution() method so it bypasses any event as we want to secure them.

#### Interceptor Test

So to make sure this works, here is our Interceptor Test Case with all possibilities for our Security Interceptor.

```js
component extends="coldbox.system.testing.BaseInterceptorTest" interceptor="simpleMVC.interceptors.SimpleSecurity"{

	function setup(){
		super.setup();
		// mock security service
		mockSecurityService = getMockBox().createEmptyMock("simpleMVC.model.SecurityService");
		// inject mock into interceptor
		interceptor.$property("securityService","variables",mockSecurityService);
	}

	function testPreProcess(){
		var mockEvent = getMockRequestContext();

		// TEST A
		// test already logged in and mock authorize so we can see if it was called
		mockSecurityService.$("isLoggedIn",true).$("authorize",false);
		// call interceptor
		interceptor.preProcess(mockEvent,{});
		// verify nothing called
		assertTrue( mockSecurityService.$never("authorize") );

		// TEST B
		// test NOT logged in and NO credentials, so just challenge
		mockSecurityService.$("isLoggedIn",false).$("authorize",false);
		// mock incoming headers and no auth credentials
		mockEvent.$("getHTTPHeader").$args("Authorization").$results("")
			.$("getHTTPBasicCredentials",{username="",password=""})
			.$("setHTTPHeader");
		// call interceptor
		interceptor.preProcess(mockEvent,{});
		// verify authorize called once
		assertTrue( mockSecurityService.$once("authorize") );
		// Assert Set Header
		AssertTrue( mockEvent.$once("setHTTPHeader") );
		// assert renderdata
		assertEquals( "401", mockEvent.getRenderData().statusCode );

		// TEST C
		// Test NOT logged in With basic credentials that are valid
		mockSecurityService.$("isLoggedIn",false).$("authorize",true);
		// reset mocks for testing
		mockEvent.$("getHTTPBasicCredentials",{username="luis",password="luis"})
			.$("setHTTPHeader");
		// call interceptor
		interceptor.preProcess(mockEvent,{});
		// Assert header never called.
		AssertTrue( mockEvent.$never("setHTTPHeader") );
	}


}
```

As you can see from our A,B, anc C tests that we use [MockBox](http://wiki.coldbox.org/wiki/MockBox.cfm) to mock the security service, the request context and methods so we can build our interceptor without knowledge of other parts.

### Security Service

Now that we have our interceptor built and fully tested, let's build the security service.

```js
component accessors="true" singleton{

	// Dependencies
	property name="sessionStorage" 	inject="coldbox:plugin:SessionStorage";

	/**
	* Constructor
	*/
	public SecurityService function init(){

		variables.username = "luis";
		variables.password = "coldbox";

		return this;
	}

	/**
	* Authorize with basic auth
	*/
	function authorize(username,password){

		// Validate Credentials, we can do better here
		if( variables.username eq username AND variables.password eq password ){
			// Set simple validation
			sessionStorage.setVar("userAuthorized",  true );
			return true;
		}

		return false;
	}

	/**
	* Checks if user already logged in or not.
	*/
	function isLoggedIn(){
		if( sessionStorage.getVar("userAuthorized","false") ){
			return true;
		}
		return false;
	}

}
```

We use a simple auth with luis and coldbox as the password. Of course, you would get fancy and store these in a database and have a nice object model around it. For this purposes was having a simple 1 time username and password. We also use the SessionStorage plugin in order to interact with the session scope with extra pizass and the most important thing: We can mock it!

#### Security Service Test

```js
component extends="coldbox.system.testing.BaseModelTest" model="simpleMVC.model.SecurityService"{

	function setup(){
		super.setup();
		// init model
		model.init();
		// mock dependency
		mockSession = getMockBox().createEmptyMock("coldbox.system.plugins.SessionStorage");
		model.$property("sessionStorage","variables",mockSession);
	}

	function testauthorize(){

		// A: Invalid
		mockSession.$("setVar");
		r = model.authorize("invalid","invalid");
		assertFalse( r );
		assertTrue( mockSession.$never("setVar") );

		// B: Valid
		mockSession.$("setVar");
		r = model.authorize("luis","coldbox");
		assertTrue( r );
		assertTrue( mockSession.$once("setVar") );


	}

	function testIsLoggedIn(){

		// A: Invalid
		mockSession.$("getVar",false);
		assertFalse( model.isLoggedIn() );

		// B: Valid
		mockSession.$("getVar",true);
		assertTrue( model.isLoggedIn() );
	}

}
```

Again, you can see that now we use our BaseModelTest case and continue to use mocks for our dependencies.

### Interceptor Declaration

We are pretty much done, so we can open our [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) in our config/Coldbox.cfc and look for the interceptors section as we will be adding our simple security interceptor.

```js
//Register interceptors as an array, we need order
interceptors = [
	//Autowire
	{class="coldbox.system.interceptors.Autowire"},
	//SES
	{class="coldbox.system.interceptors.SES"},
	// Security
	{ class="interceptors.SimpleSecurity" }
];
```

We just add our Simple Security interceptor and reinit your app via [URLActions](http://wiki.coldbox.org/wiki/URLActions.cfm) or append fwreinit=1 to the URL and you should be now using simple security.
