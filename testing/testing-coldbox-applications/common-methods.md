# Common Testing Methods

## BaseSpec

It is important to get familiar with TestBox before adventuring into ColdBox testing. Therefore, we suggest you reference the TestBox Docs \([https://testbox.ortusbooks.com/primers/testbox-bdd-primer](https://testbox.ortusbooks.com/primers/testbox-bdd-primer)\) or the TestBox API Docs \([http://apidocs.ortussolutions.com/testbox/current](http://apidocs.ortussolutions.com/testbox/current)\).  Below you can see a few of the common methods available to you.

```javascript
// quick assertion methods
assert( expression, [message] )
fail(message)

// Life Cycle Methods
beforeEach( body )
afterEach( body )
aroundEach( body, spec )

// Spec Methods
describe( title, body, labels, asyncAll, skip )
xdescribe( title, body, labels, asyncAll )
fdescribe( title, body, labels, asyncAll )

feature( title, body, labels, asyncAll, skip )
xfeature( title, body, labels, asyncAll )
ffeature( title, body, labels, asyncAll )


it( title, body, labels, skip )
xit( title, body, labels )
fit( title, body, labels, skip )

// Expectations
expect( actual )

// extensions
addMatchers( matchers )
addAssertions( assertions )

// runners
runRemote( testSpecs, testSuites, deubg, reporter );

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

As we have seen that our BaseTestCase inherits from the BaseSpec you get all the goodness of TestBox. However, the BaseTestCase also has a wealth of methods to assist in your testing adventures: [https://apidocs.ortussolutions.com/coldbox/6.0.0/coldbox/system/testing/BaseTestCase.html](https://apidocs.ortussolutions.com/coldbox/6.0.0/coldbox/system/testing/BaseTestCase.html)

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

