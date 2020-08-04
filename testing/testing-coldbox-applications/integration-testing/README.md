# Integration Testing

## Integration Testing

We will begin our adventure with integration testing. Integration testing allows you to test a real life request to your application without using a browser. Your test bundle CFC will load a new virtual application for you to test each specification under it; all aspects of your application are loaded: caching, dependency injection, AOP, etc. Then you can target an event to test and verify its behavior accordingly. First of all, these type of tests go in your `integration` folder of your test harness or in the specific module folder if you are testing modules.

### Basics

![](../../../.gitbook/assets/handlertotestrelationship%20%281%29.png)

Here are the basics to follow for integration testing:

* Create one test bundle CFC for each event handler you would like to test or base it off your BDD requirements
* Bundle CFC inherits from `coldbox.system.testing.BaseTestCase`
* The bundle CFC can have some annotations that tell the testing framework to what ColdBox application to connect to and test
* Execution of the event is done via the `execute()` method, which returns a request context object
* Execution of API requests can be done via the convenience `request()` method or the HTTP method aliases: `get(), post(), put(), patch(), head(), options()`
* Most verifications and assertions are done via the contents of the request context object \(request collections\)

```javascript


			it( "can do a relocation", function() {
				var event = execute( event = "main.doSomething" );
				expect( event.getValue( "relocate_event", "" ) ).toBe( "main.index" );
			} );

			it( "can startup executable code", function() {
				var event = execute( "main.onAppInit" );
			} );

			it( "can handle exceptions", function() {
				// You need to create an exception bean first and place it on the request context FIRST as a setup.
				var exceptionBean = createMock( "coldbox.system.web.context.ExceptionBean" ).init(
					erroStruct   = structNew(),
					extramessage = "My unit test exception",
					extraInfo    = "Any extra info, simple or complex"
				);
				prepareMock( getRequestContext() ).setValue(
						name    = "exception",
						value   = exceptionBean,
						private = true
					)
					.$( "setHTTPHeader" );

				// TEST EVENT EXECUTION
				var event = execute( "main.onException" );
			} );

			describe( "Request Events", function() {
				it( "fires on start", function() {
					var event = execute( "main.onRequestStart" );
				} );

				it( "fires on end", function() {
					var event = execute( "main.onRequestEnd" );
				} );
			} );

			describe( "Session Events", function() {
				it( "fires on start", function() {
					var event = execute( "main.onSessionStart" );
				} );

				it( "fires on end", function() {
					// Place a fake session structure here, it mimics what the handler receives
					URL.sessionReference     = structNew();
					URL.applicationReference = structNew();
					var event                = execute( "main.onSessionEnd" );
				} );
			} );
		} );
	}

}

```

We will explain later the life-cycle methods and the `run()` method where you will be writing your specs.

> **Hint** Please refer to our BDD primer to start: [http://testbox.ortusbooks.com/content/primers/bdd/index.html](http://testbox.ortusbooks.com/content/primers/bdd/index.html)

### CommandBox Generation

Also remember that you can use CommandBox to generate integration tests with a few simple commands:

```text
coldbox create integration-test handler=main actions=index,save,run --open
# help
coldbox create integration-test help
```

> **Info** Please also note that whenever you create a handler, interceptor or model with CommandBox it will automatically create the integration or unit test for you.

