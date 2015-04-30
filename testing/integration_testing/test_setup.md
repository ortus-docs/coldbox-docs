# Test Setup

Now that we know how to start, here is the code for a test. Please note that if you override the setup() method, you must call its parent. If not, the application will not load.

```js
component extends="coldbox.system.testing.BaseTestCase" appMapping="/apps/MyApp"{
	
	function run(){

		describe( "Main Handler", function(){

			beforeEach(function( currentSpec ){
				// Setup as a new ColdBox request, VERY IMPORTANT. ELSE EVERYTHING LOOKS LIKE THE SAME REQUEST.
				setup();
			});

			it( "+homepage renders", function(){
				var event = execute( event="main.index", renderResults=true );
				expect(	event.getValue( name="welcomemessage", private=true ) ).toBe( "Welcome to ColdBox!" );
			});
		
		});
	}

}
```

