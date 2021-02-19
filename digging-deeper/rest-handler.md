# REST handler

## RestHander & ColdBox Response

Coldbox 6 has finally integrated a base handler and a response object into the core, so developers can have even more support when building RESTful services. This new rest handler will provide you with tons of utilities and approaches to make all of your RESTFul services:

* Uniform
* Consistent
* A consistent and extensible response object
* Error handling
* Invalid Route handling
* Much more

{% hint style="info" %}
In previous versions of Coldbox support for RESTful services was provide by adding base handler and response objec to our application templates.  Integration into the core brings ease of use,  faster handling and above all: a uniform approach for each coldbox version.
{% endhint %}

![RestHandler UML](../.gitbook/assets/resthandler.png)

## Base Class

For creation of REST handlers you can now inherit from our new base class `coldbox.system.RestHandler`, directly via `extends="coldbox.system.Resthandler"` or our new `restHandler` annotation. This will give you access to our enhanced API utilities and the native response object via the request context's `getResponse()` method.

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

You can now build all of your api’s using the native response object like the rest templates, but now from the core directly. 

{% hint style="info" %}
Upgrade notes: the response object will be accessible using the `event.getResponse()` method but will still be available as `prc.response.` 
{% endhint %}

{% hint style="info" %}
If you are using cbORM make sure to check the chapter [Automatic Rest Crud](https://coldbox-orm.ortusbooks.com/orm-events/automatic-rest-crud)
{% endhint %}

The Rest handler gives you the following actions out of the box:

| **Core Actions** | **Purpose** |
| :--- | :--- |
| aroundHandler\(\) | Wraps all rest actions uniformly to provide consistency and error trapping. |
| onError\(\) | An implicit error handler is provided just in case anything explodes in your restful actions. Sends an appropriate 500 error |
| onValidationException\(\) | Traps any and makes sure it sends the appropriate 400 response with the invalid data. Useful for using **cbValidation**, for example with the `validateOrFail()` methods. |
| onEntityNotFoundException\(\) | Traps any or exceptions and makes sure it send an appropriate 404 response. Useful for leveraging **cborm** or **Quick** ORM |
| onInvalidHTTPMethod\(\) | Traps any invalid HTTP method security exception and sends the appropriate 405 not allowed response |
| onMissingAction\(\) | Traps any invalid actions/resource called in your application and sends the appropriate 404 response |
| onAuthenticationFailure\(\) | Traps InvalidCredentials exceptions and sends the appropriate 403 invalid credentials response. If you are using **cbSecurity** it will also verify jwt token expiration and change the error messages accordingly. |
| onAuthorizationFailure\(\) | Action that can be used when a user does not have authorization or access to your application or code. Usually you will call this manually or from a security library like **cbSecurity** or **cbGuard**. It will send a 401 not authorized response. |
| onInvalidRoute\(\) | Action that can be used as a catch all from your router so it can catch all routes that are invalid. It will send a 404 response accordingly. |
| onExpectationFailed\(\) | Utility method for when an expectation of the request fails \( e.g. an expected parameter is not provided \). This action is called manually from your own handlers and it will output a 417 response back to the user. |

### AroundHandler in Detail

The `aroundHandler`\(\) provided in the `RestHandler` will intercept all rest calls in order to provide consistency and uniformity to all your actions. It will try/catch for major known exceptions, time your requests, add extra output on development and much more. Here are a list of the features available to you:

* Exception Handling
  * Automatic trapping of the following exceptions: `InvalidCredentials, ValidationException, EntityNotFound, RecordNotFound`
  * Automatic trapping of other exceptions
  * Logging automatically the exception with extra restful metadata
  * If in a `development` environment it will respond with much more information necessary for debugging both in the response object and headers
* Development Responses
  * If you are in a `development` environment it will set the following headers for you:
    * `x-current-route`
    * `x-current-routed-url`
    * `x-current-routed-namespace`
    * `x-current-event`
* Global Headers
  * The following headers are sent in each request
    * `x-response-time` : The time the request took in CF
    * `x-cached-response` : If the request is cached via event caching

The `aroundHandler()` is also smart in detecting the following outputs from a handler:

* Handler `return` results
* Setting a view or layout to render
* Explicit `renderData()` calls

### Request Context Additions

* `getResponse()`
  * Will get you the current `prc.response` object, if the object doesn’t exist, it will create it and set it for you
  * The core response object can be found here: `coldbox.system.web.context.Response`

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

The response object can be found here: `coldbox.system.web.context.Response` and the rest handler constructs it by calling the request context’s `getResponse`\(\) method. The method verifies if there is a `prc.response` object and if it exists it returns it, else it creates a new one. So if you would like to use your very own, then just make sure that before the request you place your own response object in the `prc` scope.

Here is a simple example using a `preProcess()` interceptor.  Create a simple interceptor with commandbox e.g

```javascript
coldbox create interceptor name=MyInterceptor points=preProcess
```

and add the following method:

```javascript
function preProcess( event, interceptData, rc, prc ){
  prc.response = wirebox.getInstance( "MyResponseObject" );
}
```

Don't forget to register your interceptor in  `config/Coldbox.cfc:`

```javascript
		interceptors = [
			{
			class      : "interceptors.MyInterceptor",
				name       : "MyInterceptor",
				properties : {}
			}
		];
```

That’s it. Once that response object is in the `prc` scope, ColdBox will utilize it. Just make sure that your custom Response object satisfies the methods in the core one. If you want to modify the output of the response object a good place to do that would  be in the `getDataPacket()` method of your own `MyResponseObject`.  Just make sure this method will return a `struct`. 

