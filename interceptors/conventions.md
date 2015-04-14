# Conventions

What about the interception points that you keep mentioning. Well, below we will cover all of the interception points that ColdBox provides for you. However, the main convention on interceptors is that you will have to create a method with the same name as the interception point you would like to listen to. If you want it to listen to the preProcess point, you will create a preProcess(event,interceptData) method. What else? Well, this method has a return type of boolean and some arguments. So let's explore their rules.

* The interceptor method must have the same name as the interception point it is trying to listen to
* This method must accept three arguments 
    * event which is the request context object
    * interceptData which is a structure of data that the broadcaster sends
    * buffer which is a request buffer object you can use to elegantly produce content that is outputted to the user's screen
* This method must return a boolean value or nothing 
    * True means break the chain of execution
    * False or void continue execution


```js
boolean function {point}(event, interceptData, buffer);
```

The return boolean variable is very important because it tells the interceptor service whether to continue executing interceptors in the chain or not. Ok, what chain, well you can have as many interceptor objects listening to the same interception point. So if interceptors are declared like so:

* interceptor X - preProcess
* interceptor Y - preProcess
* interceptor Z - preProcess


Let's say that interceptor Y returns a true from the *preProcess* method, then interceptor Z will never get executed, the chain is broken now.

```js
component extends="coldbox.system.Interceptor"{

	function configure(){}

	boolean function afterConfigurationLoad(event,interceptData,buffer){
		if( getProperty('interceptorCompleted') eq false){
			parseAndSet();	
			setProperty('interceptorCompleted',true);
		}
		
		return false;
	}
}
```

Also remember that all interceptors are created by [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm), so you can use dependency injection, configuration binder's, and even [AOP](http://wiki.coldbox.org/wiki/WireBox-AOP.cfm) on interceptor objects. Here is a more complex sample:

HTTP Security Example: 

```js
/**
* Intercepts with HTTP Basic Authentication
*/
component {
	
	// Security Service
	property name="securityService" inject="id:SecurityService";
		
	void function configure(){
		if( !propertyExists("enabled") ){
			setProperty("enabled", true );
		} 
	}
	
	void function preProcess(event,struct interceptData, buffer){
		
		// verify turned on
		if( !getProperty("enabled") ){ return; }

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

