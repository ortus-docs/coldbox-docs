# Basic HTTP Authentication Interceptor

## Introduction

In this recipe we will create a simple interceptor that will be in charge of challenging users with HTTP Basic Authentication. It features the usage of all the new RESTful methods in our Request Context that will make this interceptor really straightforward. We will start by knowing that this interceptor will need a security service to verify security, so we will also touch on this.

## Application Setup

Let's start building the app using CommandBox and installing all the necessary dependencies:

```bash
# create our folder
mkdir simple-auth --cd
# generate a coldbox app
coldbox create app "SimpleAuth"
# Install extra dependencies
install cbStorages
# Startup the server
server start
```

## Security Service

Let's build a simple security service to track users. Use CommandBox to generate the service model with two functions and let's mark it as a singleton:

```bash
coldbox create model SecurityService authorize,isLoggedIn singleton
```

This will create the `models/SecurityService` and the companion unit tests. Let's fill them out:

### Security Service Test

```javascript
/**
* The base model test case will use the 'model' annotation as the instantiation path
* and then create it, prepare it for mocking and then place it in the variables scope as 'model'. It is your
* responsibility to update the model annotation instantiation path and init your model.
*/
component extends="coldbox.system.testing.BaseModelTest" model="models.SecurityService"{

	/*********************************** LIFE CYCLE Methods ***********************************/

	function beforeAll(){
		super.beforeAll();

		// setup the model
		super.setup();

		mockSession = createMock( "modules.cbstorages.models.SessionStorage" )
	}

	function afterAll(){
		super.afterAll();
	}

	/*********************************** BDD SUITES ***********************************/

	function run(){

		describe( "SecurityService Suite", function(){

			beforeEach(function( currentSpec ){
				model.init();
				model.setSessionStorage( mockSession );
			});

			it( "can be created (canary)", function(){
				expect( model ).toBeComponent();
			});

			it( "should authorize valid credentials", function(){
				mockSession.$( "set", mockSession );
				expect( model.authorize( "luis", "coldbox" ) ).toBeTrue();
			});

			it( "should not authorize invalid credentials ", function(){
				expect( model.authorize( "test", "test" ) ).toBeFalse();
			});

			it( "should verify if you are logged in", function(){
				mockSession.$( "get", true );
				expect( model.isLoggedIn() ).toBeTrue();
			});

			it( "should verify if you are NOT logged in", function(){
				mockSession.$( "get", false );
				expect( model.isLoggedIn() ).toBeFalse();
			});

		});

	}

}

```

### Security Service

```javascript
component accessors="true" singleton{

    // Dependencies
    property name="sessionStorage" inject="SessionStorage@cbStorages";

    /**
     * Constructor
     */
    function init(){
		    // Mock Security For Now
        variables.username = "luis";
        variables.password = "coldbox";

        return this;
    }

    /**
     * Authorize with basic auth
     */
    function authorize( username, password ){

        // Validate Credentials, we can do better here
        if( variables.username eq username AND variables.password eq password ){
            // Set simple validation
            sessionStorage.set( "userAuthorized", true );
            return true;
        }

        return false;
    }

    /**
     * Checks if user already logged in or not.
     */
    function isLoggedIn(){
		    return sessionStorage.get( "userAuthorized", "false" );
    }

}
```

Please note that we are using a hard coded username and password, but you can connect this to any provider or db.

## The Interceptor

Let's generate the interceptor now and listen to   `preProcess`

```bash
coldbox create interceptor name="SimpleSecurity" points="preProcess"
```

The `preProcess`listener is to listen to all incoming requests are inspected for security.  Please note that the unit test for this interceptor is also generated.  Let's fill out the interceptor test first:

### Interceptor Test

So to make sure this works, here is our Interceptor Test Case with all possibilities for our Security Interceptor.

```javascript
component extends="coldbox.system.testing.BaseInterceptorTest" interceptor="interceptors.SimpleSecurity" {

	/*********************************** LIFE CYCLE Methods ***********************************/

	function beforeAll() {
		super.beforeAll();

		// mock security service
		mockSecurityService = createEmptyMock( "models.SecurityService" );
	}

	function afterAll() {
		super.afterAll();
	}

	/*********************************** BDD SUITES ***********************************/

	function run() {
		describe( "SimpleSecurity Interceptor Suite", function() {
			beforeEach( function( currentSpec ) {
				// Setup the interceptor target
				super.setup();
				// inject mock into interceptor
				interceptor.$property(
					"securityService",
					"variables",
					mockSecurityService
				);
				mockEvent = getMockRequestContext();
			} );

			it( "can be created (canary)", function() {
				expect( interceptor ).toBeComponent();
			} );

			it( "can allow already logged in users", function() {
				// test already logged in and mock authorize so we can see if it was called
				mockSecurityService.$( "isLoggedIn", true ).$( "authorize", false );
				// call interceptor
				interceptor.preProcess( mockEvent, {} );
				// verify nothing called
				expect( mockSecurityService.$never( "authorize" ) ).toBeTrue();
			} );

			it( "will challenge if you are not logged in and you don't have the right credentials", function() {
				// test NOT logged in and NO credentials, so just challenge
				mockSecurityService.$( "isLoggedIn", false ).$( "authorize", false );
				// mock incoming headers and no auth credentials
				mockEvent
					.$( "getHTTPHeader" )
					.$args( "Authorization" )
					.$results( "" )
					.$( "getHTTPBasicCredentials", { username : "", password : "" } )
					.$( "setHTTPHeader" );
				// call interceptor
				interceptor.preProcess( mockEvent, {} );
				// verify authorize called once
				expect( mockSecurityService.$once( "authorize" ) ).toBeTrue();
				// Assert Set Header
				expect( mockEvent.$once( "setHTTPHeader" ) ).toBeTrue();
				// assert renderdata
				expect( mockEvent.getRenderData().statusCode ).toBe( 401 );
			} );

			it( "should authorize if you are not logged in but have valid credentials", function() {
				// Test NOT logged in With basic credentials that are valid
				mockSecurityService.$( "isLoggedIn", false ).$( "authorize", true );
				// reset mocks for testing
				mockEvent
					.$( "getHTTPBasicCredentials", { username : "luis", password : "luis" } )
					.$( "setHTTPHeader" );
				// call interceptor
				interceptor.preProcess( mockEvent, {} );
				// Assert header never called.
				expect( mockEvent.$never( "setHTTPHeader" ) ).toBeTrue();
			} );
		} );
	}

}

```

As you can see from our A,B, anc C tests that we use MockBox to mock the security service, the request context and methods so we can build our interceptor without knowledge of other parts.

### Interceptor Code

{% code title="interceptors/SimpleSecurity.cfc" %}
```javascript
/**
 * Intercepts with HTTP Basic Authentication
 */
component {

    // Security Service
    property name="securityService" inject="provider:SecurityService";

    void function configure(){}

    void function preProcess( event, interceptData, rc, prc ){

        // Verify Incoming Headers to see if we are authorizing already or we are already Authorized
        if( !securityService.isLoggedIn() OR len( event.getHTTPHeader( "Authorization", "" ) ) ){

            // Verify incoming authorization
            var credentials = event.getHTTPBasicCredentials();
            if( securityService.authorize(credentials.username, credentials.password) ){
                // we are secured woot woot!
                return;
            };

            // Not secure!
            event.setHTTPHeader(
				name 	= "WWW-Authenticate",
				value 	= "basic realm=""Please enter your username and password for our Cool App!"""
			);

            // secured content data and skip event execution
			event
				.renderData(
					data 		= "<h1>Unathorized Access<p>Content Requires Authentication</p>",
					statusCode 	= "401",
					statusText 	= "Unauthorized"
				)
                .noExecution();
        }

    }

}
```
{% endcode %}

As you can see it relies on a `SecurityService` model object that is being wired via:

```javascript
// Security Service
property name="securityService" inject="id:SecurityService";
```

Then we check if a user is logged in or not and if not we either verify their incoming HTTP basic credentials or if none, we challenge them by setting up some cool headers and bypass event execution:

```javascript
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

The `renderData()` is essential in not only setting the **401** status codes but also concatenating to a `noExecution()` method so it bypasses any event as we want to secure them.

## Interceptor Declaration

Open your `Coldbox.cfc` configuration file and add it.

```javascript
//Register interceptors as an array, we need order
interceptors = [
    // Security
    { class="interceptors.SimpleSecurity" }
];
```

Now reinit your app `coldbox reinit` and you are simple auth secured!

## Why provider

You might have noticed the injection of the security service into the interceptor used a `provider:` DSL prefix, why? Well, global interceptors are created first, then modules are loaded, so if we don't use a `provider` to delay the injection, then the storages module might not be loaded yet.

## Extra Credit

Now that the hard part is done, we encourage you to try and build the integration test for the application now.  Please note that most likely you would NOT do the unit test for the interceptor if you do the integration portion in BDD Style.

