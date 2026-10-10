---
description: >-
  Drive a real browser against your running ColdBox application with
  BaseTestCase, the @browser annotation, TestBox browser support and
  bx-playwright. BoxLang only.
icon: browser
---

# Browser Testing

{% hint style="warning" %}
🚀 **BoxLang Exclusive**: browser testing requires **BoxLang** and the **bx-playwright** module. Browser specs are BoxLang classes (`.bx`) and TestBox only attaches browser support on BoxLang, so CFML engines (Lucee, Adobe ColdFusion) cannot run them. Keep your browser specs in their own folder, such as `tests/specs/browser`, and exclude that folder from the runner on other engines. On BoxLang without bx-playwright, browser specs are **skipped** with an install hint instead of failing.
{% endhint %}

Integration tests with `BaseTestCase` simulate a request inside a virtual ColdBox application: they are fast and precise, but no HTML is ever parsed, no JavaScript runs and no cookie travels between requests. Browser tests close that gap. Add the `@browser` annotation to a `coldbox.system.testing.BaseTestCase` spec and it drives a real Chromium, Firefox or WebKit browser, through [bx-playwright](https://bxplaywright.boxlang.io), against your **running** application, so you can test what your users actually see: forms, redirects, sessions, JavaScript widgets and full login flows.

```javascript
@appMapping( "/root" )
@browser
@baseURL( "http://127.0.0.1:8080" )
class extends="coldbox.system.testing.BaseTestCase" {

	function beforeAll() {
		super.beforeAll()
		// Log in once through the login page, reused by every browse() call with { session : "admin" }
		this.playwright().session( "admin", ( page ) => {
			visitRoute( page, "login" ).fill( "Email", "admin@example.com" ).fill( "Password", "secret" ).click( "Sign in" )
		} )
	}

	function run() {
		describe( "Users", () => {
			it( "shows a user profile to a logged in admin", () => {
				browse( ( page ) => {
					visitRoute( page, "users.show", { id : 5 } )
					assertRouteIs( page, "users.show" )
					expect( page ).toSee( "User 5" )
				}, { session : "admin" } )
			} )
		} )
	}

}
```

## How It Works

There is no special base class for browser tests. A browser spec is a regular `BaseTestCase` spec with a browser annotation, so it **still loads your ColdBox application virtually**, exactly like an integration test. It uses that virtual application to know your routes, module entry points and settings. The browser itself talks to a real web server that serves your application.

```mermaid
flowchart LR
    Spec["BaseTestCase spec<br/>with @browser"] -->|appMapping| Virtual["Virtual ColdBox app<br/>(routes, settings)"]
    Spec -->|browse()| PW["bx-playwright<br/>Chromium"]
    PW -->|HTTP| Server["Running ColdBox app<br/>(baseURL)"]
```

* **TestBox** provides the browser support, turned on by the `@browser` annotation (see [Turning On Browser Support](#turning-on-browser-support)): `browse()`, one shared browser per bundle, isolated pages, browser matchers such as `toSee()` and `toHaveTitle()`, and screenshots and traces attached to failed specs.
* **bx-playwright** provides the browser and its fluent API: `page.visit()`, `fill()`, `click()`, locators and retrying assertions.
* **ColdBox** adds what only the framework knows: [named routes](named-routes.md) (`routeURL()`, `visitRoute()`, `assertRouteIs()`), built into `BaseTestCase`. For pages behind a login, bx-playwright [saved sessions](authentication.md) log in once through your real login page and reuse the session.

## Turning On Browser Support

TestBox attaches browser support to **any** spec whose class, or a class it extends, has one of these annotations:

| Annotation | Description |
| --- | --- |
| `@browser` | Turns browser support on with the bx-playwright default profile |
| `@browserProfile( "ci" )` | Turns it on with bx-playwright profiles for the bundle browser, a list such as `ci,mobile` |
| `@baseURL( "http://127.0.0.1:8080" )` | Turns it on with the URL relative visits resolve against |

The annotations are **inherited**: put them on a base class of your own and every spec that extends it can browse. `@browser` alone is enough when the base URL comes from elsewhere (see [Setup](setup.md#run-your-application)), but it reads well to keep it next to `@baseURL` or `@browserProfile`.

When a spec has one of them, the TestBox runner (BoxLang only) mixes into the spec's `this` and `variables` scopes, so you call them unqualified:

* `browse( callback, [options] )`, `browserAvailable()`, `ensureBrowserInstalled()`, `getBrowserSupport()` and `closeBrowser()`
* `this.playwright()`, the bx-playwright manager of the bundle (an unqualified `playwright()` is still the bx-playwright BIF)
* The TestBox browser matchers, such as `toSee()` and `toHaveTitle()`

Methods the spec declares itself are kept. The runner closes the bundle browser after the bundle, even when `afterAll()` throws.

`browse()` works in any annotated spec, including `BaseModelTest` or `BaseInterceptorTest` specs. The ColdBox route helpers `routeURL()`, `visitRoute()` and `assertRouteIs()` live in `BaseTestCase` and build URLs from the loaded application, so they need a spec that loads ColdBox: a `BaseTestCase` with `loadColdBox` left at its default of `true`.

{% hint style="warning" %}
Browser specs need a TestBox release with annotation-driven browser support ([TestBox#222](https://github.com/Ortus-Solutions/TestBox/pull/222)). On older TestBox releases the annotations do nothing and `browse()` is not defined.
{% endhint %}

## Integration Tests or Browser Tests?

Use both. They answer different questions.

| | `BaseTestCase` integration tests | `BaseTestCase` + `@browser` browser tests |
| --- | --- | --- |
| Runs | Inside a virtual application, no web server | A real browser against your running server |
| Speed | Milliseconds per spec | Hundreds of milliseconds to seconds per spec |
| Sees | The request context: `rc`, `prc`, rendered content, handler results | The rendered page: text, visibility, URLs, form values |
| JavaScript, CSS, cookies | No | Yes |
| Best for | Handler logic, APIs, relocations, security rules | User journeys, forms, front-end behavior, smoke tests |

A good suite has many integration tests and a smaller set of browser tests that cover the journeys that matter most.

## The Guide

| Page | What you will learn |
| --- | --- |
| [Setup](setup.md) | Install bx-playwright and a browser, the version requirements, and how to run your application for the browser |
| [Writing Browser Tests](writing-browser-tests.md) | A complete spec, `browse()`, the class annotations and the browser matchers |
| [Named Routes](named-routes.md) | `routeURL()`, `visitRoute()` and `assertRouteIs()`, including module routes |
| [Authentication](authentication.md) | Logged-in tests with bx-playwright saved sessions |
| [Artifacts & Debugging](artifacts-and-debugging.md) | Screenshots and traces of failed specs, the trace viewer and the `debug` profile |
| [Continuous Integration](continuous-integration.md) | A complete GitHub Actions workflow |
| [Troubleshooting](troubleshooting.md) | Common errors and how to fix them |

## See Also

* [Integration Testing](../integration-testing/README.md)
* [Testing Classes](../coldbox-testing-classes.md)
* [bx-playwright documentation](https://bxplaywright.boxlang.io)
* [TestBox Browser Testing guide](https://testbox.ortusbooks.com/browser-testing)
