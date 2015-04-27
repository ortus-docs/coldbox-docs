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

## Executing The Runner

To execute your application template tests and the generated tests just browse to the URL: `http://127.0.0.1:{port}/tests/runner.cfm` and you will get a full integration report:

![](../images/overview_testing.png)

Everything is already pre-wired for you and ready for you to do full life-cycle integration testing.  Don't believe me? Try it out, go change your `echo()` action to the following code and re-run your test:

```js
function echo(event,rc,prc){
	event.setFunkyView("hello/echo");
}	
```

You will get an error now: **component [coldbox.system.web.context.RequestContext] has no function with name [setFunkyView]**. Fix it and re-run it. Ok, hold on to something..... You are now doing live integration testing my friend.  Simple, but yet accomplishing.

## CommandBox Runner

You can also execute the runner via CommandBox. So let's tell CommandBox where your runner is located:

```bash
