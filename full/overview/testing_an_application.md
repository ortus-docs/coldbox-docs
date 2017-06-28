# Testing An Application

ColdBox tightly integrates with [TestBox](http://www.ortussolutions.com/products/testbox), the Behavior Driven Development Testing Framework for ColdFusion (CFML).  We can easily do unit and integration testing for our application.  To start, let's install TestBox via CommandBox

```bash
install testbox --saveDev
```

Please note the `--saveDev` flag we used. This tells CommandBox that this dependency is only for testing purposes and not for distribution or production usage.  Check out the `box.json` under the `devDependencies`.


## Test Harness
Every ColdBox application template comes with a pre-set testing harness under the `/tests` folder:

```
Dir           0 Apr 25,2015 11:04:11 resources
Dir           0 Apr 25,2015 11:04:11 results
Dir           0 Apr 25,2015 11:04:11 specs
File        817 Apr 13,2015 18:04:34 Application.cfc
File        693 Jan 15,2015 14:01:18 runner.cfm
File       5164 Jan 15,2015 14:01:20 test.xml
```

Every harness has its own unique `Application.cfc` which must mimic your application's settings.  It also comes with an HTML runner called `runner.cfm` and an ANT runner called `test.xml`.  All your test bundles and specifications will go under the `specs` directory and in the appropriate sub-directory:

```
Dir           0 Apr 27,2015 11:04:11 integration
Dir           0 Apr 25,2015 11:04:11 modules
Dir           0 Dec 16,2013 19:12:32 unit
File          0 Jan 15,2015 14:01:18 all_tests_go_here.txt
```

Under the `integration` tests you will find the test bundles that come with the application template and the ones we generated:

```
File       1857 Apr 27,2015 11:04:11 helloTest.cfc
File       3236 Jan 21,2015 16:01:56 MainBDDTest.cfc
File       4398 Jan 15,2015 14:01:20 MainTest.cfc
```

The `helloTest` BDD test bundle can be show below:

```js
/*******************************************************************************
*	Integration Test as BDD (CF10+ or Railo 4.1 Plus)
*
*	Extends the integration class: coldbox.system.testing.BaseTestCase
*
*	so you can test your ColdBox application headlessly. The 'appMapping' points by default to 
*	the '/root' mapping created in the test folder Application.cfc.  Please note that this 
*	Application.cfc must mimic the real one in your root, including ORM settings if needed.
*
*	The 'execute()' method is used to execute a ColdBox event, with the following arguments
*	* event : the name of the event
*	* private : if the event is private or not
*	* prePostExempt : if the event needs to be exempt of pre post interceptors
*	* eventArguments : The struct of args to pass to the event
*	* renderResults : Render back the results of the event
*******************************************************************************/
component extends="coldbox.system.testing.BaseTestCase" appMapping="/"{
	
	/*********************************** LIFE CYCLE Methods ***********************************/

	function beforeAll(){
		super.beforeAll();
		// do your own stuff here
	}

	function afterAll(){
		// do your own stuff here
		super.afterAll();
	}

	/*********************************** BDD SUITES ***********************************/
	
	function run(){

		describe( "hello Suite", function(){

			beforeEach(function( currentSpec ){
				// Setup as a new ColdBox request for this suite, VERY IMPORTANT. ELSE EVERYTHING LOOKS LIKE THE SAME REQUEST.
				setup();
			});

			it( "index", function(){
				var event = execute( event="hello.index", renderResults=true );
				// expectations go here.
				
			});

			it( "echo", function(){
				var event = execute( event="hello.echo", renderResults=true );
				// expectations go here.
				
			});

		
		});

	}

}

```

## Executing The Runner

To execute your application template tests and the generated tests just browse to the URL: `http://127.0.0.1:{port}/tests/runner.cfm` and you will get a full integration report:

![](/images/overview_testing.png)

Everything is already pre-wired for you and ready for you to do full life-cycle integration testing.  Don't believe me? Try it out, go change your `echo()` action to the following code and re-run your test:

```js
function echo(event,rc,prc){
	event.setFunkyView("hello/echo");
}	
```

You will get an error now: **component [coldbox.system.web.context.RequestContext] has no function with name [setFunkyView]**. Fix it and re-run it. Ok, hold on to something..... You are now doing live integration testing my friend.  Simple, but yet accomplishing.

## CommandBox Runner

You can also execute the runner via CommandBox. So let's tell CommandBox where your runner is located, just change the `{port}` to your port assigned.

```bash
package set testbox.runner=http://127.0.0.1:{port}/tests/runner.cfm
```

Then use the `testbox run` command:

```bash
testbox run
```

You will then execute your tests and get a text report from it.

## What's Next

We have a fully dedicated section on [testing](//testing/index.md), please visit it for in-depth information.



