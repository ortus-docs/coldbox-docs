# Request Setup\(\)

Since we are simulating a browser, the testing framework needs to know when to prepare a new virtual request internally.  This is achieved via the `setup()` method provided to us via the `BaseTestCase` object.  This method internally will simulate a new virtual request, if **NOT**, **every test case will be treated as if you are in the same request.**

The best way to accomplish this is to leverage the `beforeEach()` life-cycle event method.  This makes sure that each spec execution is a new virtual request.

```javascript
	function run() {
		describe( "Main Handler", function() {
			
			beforeEach( function( currentSpec ) {
				// Setup as a new ColdBox request, VERY IMPORTANT. ELSE EVERYTHING LOOKS LIKE THE SAME REQUEST.
				setup();
			} );

			it( "can render the homepage", function() {
				var event = this.get( "main.index" );
				expect( event.getValue( name = "welcomemessage", private = true ) ).toBe( "Welcome to ColdBox!" );
			} );

			it( "can render some restful data", function() {
				var event = this.post( "main.data" );

				debug( event.getHandlerResults() );
				expect( event.getRenderedContent() ).toBeJSON();
			} );
		});
		
	}
```

