# Common Testing Methods

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

