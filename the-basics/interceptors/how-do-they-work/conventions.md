# Conventions

By convention, any interceptor CFC must create a method with the same name as the event they want to listen to. This method has a return type of `boolean` and receives 5 arguments. So let's explore their rules.

```javascript
boolean function {point}( event, data, buffer, rc, prc );
```

## Arguments

* `event` which is the request context object
* `data` which is a structure of data that the broadcaster sends
* `buffer` which is a request buffer object you can use to elegantly produce content that is outputted to the user's screen
* `rc` reference to the request collection struct
* `prc` reference to the private request collection struct

## Return type

The intercepting method returns `boolean` or `void`. If boolean then it means something:

* **True** means break the chain of execution, so no other interceptors in the chain will fire.
* **False** or `void` continue execution

```javascript
component extends="coldbox.system.Interceptor"{

    function configure(){}

    boolean function afterConfigurationLoad(event,data,buffer){
        if( getProperty('interceptorCompleted') eq false){
            parseAndSet();    
            setProperty('interceptorCompleted',true);
        }

        return false;
    }
}
```

Also remember that all interceptors are created by WireBox, so you can use dependency injection, configuration binder's, and even [AOP](http://wirebox.ortusbooks.com) on interceptor objects. Here is a more complex sample:

**HTTP Security Example:**

```javascript
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

    void function preProcess(event,struct data, buffer){

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

