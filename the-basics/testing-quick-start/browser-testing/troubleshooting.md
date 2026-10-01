---
description: >-
  Fix common browser testing problems: skipped specs, login 404s, wrong route
  paths, timeouts and browsers that do not start.
icon: life-ring
---

# Troubleshooting

Start with `bxPlaywright doctor`: it checks the driver, Node.js and the installed browsers. Then find your symptom below.

## Every Browser Spec Is Skipped

The browser methods skip the running spec with a reason when browser testing is not available. Read the reason in the skipped spec:

| Reason | Fix |
| --- | --- |
| `Browser specs need the BoxLang engine` | The tests run on Lucee or Adobe ColdFusion. Run them on BoxLang, or exclude your browser folder on other engines (see [Setup](setup.md#running-on-several-engines)) |
| `bx-playwright is not installed: install-bx-module bx-playwright` | The module is missing in the runtime **that runs the tests**. For a CommandBox server, install it in the server, then restart it |

## The Bundle Does Not Compile on CFML

`BrowserTestCase` is a BoxLang class. A bundle that extends it cannot load on a CFML engine, so the runner reports an error instead of a skip. Keep browser specs in their own folder and exclude it on CFML engines, like the ColdBox platform does in its `tests/runner.cfm`.

## Executable Doesn't Exist / Browser Fails to Start

The browser is not installed, or its system libraries are missing:

```bash
bxPlaywright install chromium
# Linux, CI or containers: also install the operating system libraries
bxPlaywright install chromium --with-deps
```

The browsers live in `~/.boxlang/playwright` of the user that **runs the tests**. If your server runs as another user (a service account, a container), install them as that user, or point both to the same folder with the `BX_PLAYWRIGHT_HOME` environment variable.

## loginAs() Answers 404 Not Found

`BrowserTestCase.BrowserTestingUnavailable: loginAs() failed calling [/__browser-testing/login/1/]. The endpoint answered 404 Not Found`

The running application refused the request. A `404` is deliberate: the module never tells a caller which check failed. Check them one by one on the **running** application:

1. **Environment**: the application runs in the `testing` environment. Dump `getSetting( "environment" )` in a view, or check that `ENVIRONMENT=testing` is set for the server process. Reinitialize the application (`?fwreinit=1`) after changing it.
2. **Enabled**: `moduleSettings.browserTesting.enabled` is `true` in that environment.
3. **Token**: the running application and the test runner see the **same** `BROWSER_TESTING_TOKEN`. A server started before you exported the variable does not see it.
4. **Closure**: the `login` (or `logout`) closure is set.

See [Authentication](authentication.md#security-model) for the full security model.

## loginAs() Says the Token Is Empty

`moduleSettings.browserTesting.token is empty in the test application`

`loginAs()` reads the token from the **virtual** application your spec loads with `appMapping`. That application detects its own environment: make sure the test runner also sees `ENVIRONMENT=testing` and `BROWSER_TESTING_TOKEN`, or that your configuration enables the module for the environment the tests run in.

## The User Is Not Logged In After loginAs()

* Did you log in on the right page? `loginAs()` logs in the **page you pass**: every other page of the same `browse()` call has its own cookies.
* Does your login closure persist the login in the session or a cookie? The endpoint response sets the cookies on the page context; a login stored in `prc` is gone at the end of the request.
* Do the endpoint and the next visit use the same host? A cookie set for `127.0.0.1` is not sent to `localhost`. Use one host name in `baseURL` and your SES base URL.

## Route Paths Are Wrong

`visitRoute()` lands on a 404 page, or `assertRouteIs()` fails with a path that has an extra or missing prefix such as `/index.cfm`.

`routeURL()` builds paths from the SES base URL of the **virtual** application. When the server under test uses another prefix, set the one the browser needs in a `beforeEach()`:

```javascript
beforeEach( ( currentSpec ) => {
	getRequestContext().setSESBaseURL( "http://127.0.0.1:8080/index.cfm" )
} )
```

Check what a route builds with `debug( routeURL( "users.show", { id : 5 } ) )`.

## Unknown Named Route

`InvalidArgumentException: The named route 'users.show' does not exist`

The route name is not registered in the virtual application: check the spelling, the `.as()` name in your router, and for module routes the `name@module` or `module:name` form with the module name (not its entry point).

## Timeouts

A matcher or `assertRouteIs()` waits up to the bx-playwright assertion timeout (5 seconds by default), actions and navigation up to 30 seconds. A timeout usually means the page never reached the expected state: open the trace (see [Artifacts & Debugging](artifacts-and-debugging.md)) before raising the timeouts. If the application is really slow, raise them in the bx-playwright `timeouts` setting or a custom profile.

## Matchers Are Not Found

`toSee()` and the other browser matchers are registered by `BrowserTestCase`. In a spec that extends another base class, register them yourself:

```javascript
addMatchers( new testbox.system.browser.BrowserMatchers() )
```

## The Browser Stays Open or Specs Interfere

* Use `this.playwright()`, not `playwright()`, to reach the bundle manager. The bare `playwright()` BIF creates a new manager that the bundle does not close.
* Do not use `asyncAll` in suites that browse: browser specs are not thread safe.
* Each `browse()` call gets new pages and contexts. If state leaks between specs, it lives in your application (database, cache, application scope), not in the browser.

## See Also

* [Setup](setup.md)
* [Authentication](authentication.md)
* [Artifacts & Debugging](artifacts-and-debugging.md)
* [bx-playwright Errors](https://bxplaywright.boxlang.io/errors/)
