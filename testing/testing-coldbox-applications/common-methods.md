# Testing Methods

## BaseSpec

It is important to get familiar with TestBox before adventuring into ColdBox testing. Therefore, we suggest you reference the TestBox Docs ([https://testbox.ortusbooks.com/primers/testbox-bdd-primer](https://testbox.ortusbooks.com/primers/testbox-bdd-primer)) or the TestBox API Docs ([http://apidocs.ortussolutions.com/testbox/current](http://apidocs.ortussolutions.com/testbox/current)).  Below you can see a few of the common methods available to you.

{% hint style="info" %}
We highly encourage you to use expectations instead of assertions in order to use more human and fluent syntax: [https://testbox.ortusbooks.com/in-depth/expectations](https://testbox.ortusbooks.com/in-depth/expectations)
{% endhint %}

```cfscript
// quick assertion methods
assert( expression, [message] )
expect( actual ).toBe( value )
fail(message)

// Life Cycle Methods
beforeEach( body )
afterEach( body )
aroundEach( body, spec )

// Spec Grouping Methods
describe( title, body, labels, asyncAll, skip )
feature( title, body, labels, asyncAll, skip )
story( title, body, labels, asyncAll, skip )
scenario( title, body, labels, asyncAll, skip )
given( title, body, labels, asyncAll, skip )
when( title, body, labels, asyncAll, skip )

// Spec or Tests
it( title, body, labels, skip )
then( title, body, labels, skip )

// utility methods
console( var, top )
debug( var, deepCopy, label, top )
clearDebugBuffer()
getDebugBuffer()
print( message )
printLn( message )

// mocking methods
makePublic( target, method, newName )
querySim( queryData )
getmockBox( generationPath )
createEmptyMock( className, object, callLogging )
createMock( className, object, clearMethods, callLogging )
prepareMock( object, callLogging )
createStub( callLogging, extends, implements )
getProperty( target, name, scope, defaultValue )
```

## BaseTestCase

As we have seen that our `BaseTestCase` inherits from the `BaseSpec` you get all the goodness of TestBox. However, the `BaseTestCase` also has a wealth of methods to assist in your testing adventures:&#x20;

[https://apidocs.ortussolutions.com/coldbox/current/?coldbox/system/testing/BaseTestCase.html](https://apidocs.ortussolutions.com/coldbox/current/?coldbox/system/testing/BaseTestCase.html)

{% embed url="https://apidocs.ortussolutions.com/coldbox/current/?coldbox/system/testing/BaseTestCase.html" %}

Here are some useful methods:

```javascript
announce(any state, [struct data='[runtime expression]'], [boolean async='false'], [boolean asyncAll='false'], [boolean asyncAllJoin='true'], [any asyncPriority='NORMAL'], [numeric asyncJoinTimeout='0'])

// Getters
getCache(any cacheName='default')
getCacheBox() 
getController() 
getFlashScope() 
getHandlerResults() 
getInstance([any name], [struct initArguments='[runtime expression]'], [any dsl]) 
getInterceptor(any interceptorName) 
getLogBox() 
getRenderData() 
getRenderedContent() 
getRequestContext() 
getStatusCode()
getUtil()
getWireBox()

// Get Mocking Objects
getMockController() 
getMockModel(any name, [boolean clearMethods='false'])
getMockRequestContext([boolean clearMethods='false'], [any decorator])


//Reset the persistence of the unit test coldbox app, basically removes the controller from application scope. 
reset([boolean clearMethods='false'], [any decorator]) 

// Prepare a ColdBox request
setup()
```

### ColdBox TestBox Matchers

ColdBox also automatically adds several [custom matchers](https://testbox.ortusbooks.com/in-depth/expectations/custom-matchers) to TestBox, so it can help you get nice expectations:

#### toHaveStatus()

Checks if the ColdBox response object has a matched status code.

```cfscript
expect( event.getResponse() ).toHaveStatus( statusCode )
```

#### toHaveInvalidData()

Expectation for testing against `cbValidation` invalid data fields returned in a Restful response by looking into the response object.

```cfscript
expect( event.getResponse() ).toHaveInvalidData( "username", "is required" )
expect( event.getResponse() ).toHaveInvalidData( "role", "is required" )
expect( event.getResponse() ).toHaveInvalidData( "permission", "is not unique" )
expect( event.getResponse() ).toHaveInvalidData( "status", "does not match" )
```

#### toRedirectTo()

The expectation for testing if an event returns a relocation.

```cfscript
expect( event ).toRedirectTo( "Main.index" )
```
