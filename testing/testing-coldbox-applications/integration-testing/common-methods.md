# Common Methods

The base test cases all inherit from TestBox so here are a few of the common methods you can find in every test bundle CFC \([http://testbox.ortusbooks.com/content/test\_bundles/index.html](http://testbox.ortusbooks.com/content/test_bundles/index.html)\):

```text
// quick assertion methods
assert( expression, [message] )
fail(message)

// bdd methods
beforeEach( body )
afterEach( body )
describe( title, body, labels, asyncAll, skip )
xdescribe( title, body, labels, asyncAll )
it( title, body, labels, skip )
xit( title, body, labels )
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

