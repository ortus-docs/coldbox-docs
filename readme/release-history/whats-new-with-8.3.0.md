---
description: Upcoming release
---

# What's New With 8.3.0

ColdBox 8.3.0 is a feature release that brings **real-browser testing** to ColdBox applications on BoxLang: annotate any `BaseTestCase` with `@browser` and it drives a real browser through TestBox browser support and the bx-playwright module, with named route helpers in `BaseTestCase` and logged-in tests through bx-playwright saved sessions. It also fixes module named route links.

## Major Highlights

### 🌐 Browser Testing With @browser (BoxLang)

{% hint style="warning" %}
🚀 **BoxLang Exclusive**: browser testing requires **BoxLang**, a TestBox release with annotation-driven browser support (**TestBox 7.2.0+**, [TestBox#222](https://github.com/Ortus-Solutions/TestBox/pull/222)) and the **bx-playwright** module. On older TestBox releases the annotations do nothing and `browse()` is not defined. Browser specs are BoxLang classes: on CFML engines exclude your browser specs folder from the runner. On BoxLang without bx-playwright, browser specs are skipped.
{% endhint %}

There is no separate browser test class. Annotate any `coldbox.system.testing.BaseTestCase` spec with `@browser`, `@browserProfile` or `@baseURL` (on the class or a class it extends) and TestBox attaches its browser support. The spec still loads your application virtually like any integration test and knows your routes and settings, while it drives a real Chromium, Firefox or WebKit browser against your **running** application:

```javascript
@appMapping( "/root" )
@browser
@baseURL( "http://127.0.0.1:8080" )
class extends="coldbox.system.testing.BaseTestCase" {

	function run() {
		describe( "Users", () => {
			it( "shows a user", () => {
				browse( ( page ) => {
					visitRoute( page, "users.show", { id : 5 } )
					assertRouteIs( page, "users.show" )
					expect( page ).toSee( "User 5" )
				} )
			} )
		} )
	}

}
```

* **`browse()`**, **`this.playwright()`**, **`browserAvailable()`**, `ensureBrowserInstalled()`, `getBrowserSupport()` and `closeBrowser()`, mixed into the spec by the TestBox runner: one browser per bundle, fresh isolated pages per call, closed by the runner after the bundle, even when `afterAll()` throws.
* The **`browser`**, **`baseURL`** and **`browserProfile`** class annotations, inherited from the classes your spec extends. `BaseModelTest`, `BaseInterceptorTest` or any other spec can browse the same way.
* The TestBox **browser matchers**: `toHaveTitle()`, `toHaveURL()`, `toHavePath()`, `toSee()`, `toHaveText()`, `toBeVisible()`, `toBeHidden()`, `toHaveCount()` and `toHaveValue()`, all retrying and all with `not` forms.
* Screenshots, traces and videos of failed specs **attached** to the spec in your reports.

See the [Browser Testing](../../the-basics/testing-quick-start/browser-testing/README.md) guide.

### 🧭 Named Route Helpers

`BaseTestCase` now has helpers so browser specs build URLs from your router instead of hard-coding them:

```javascript
routeURL( "users.show", { id : 5 } )               // /users/5/
visitRoute( page, "posts@blog" )                   // module routes too
assertRouteIs( page, "users.show" )                // any user
assertRouteIs( page, "users.show", { id : 5 } )    // user 5
```

`assertRouteIs()` matches the route pattern and its constraints, ignores case, the trailing slash, the query string and the hash like ColdBox routing does, and waits for redirects. See [Named Routes](../../the-basics/testing-quick-start/browser-testing/named-routes.md).

### 🔐 Logged-In Tests With Saved Sessions

Pages behind a login use bx-playwright **saved sessions**: log in once through your real login page, then start any `browse()` call already logged in. There are no test-only login endpoints, so your application ships no backdoor:

```javascript
this.playwright().session( "admin", ( page ) => {
	visitRoute( page, "login" ).fill( "Email", "admin@example.com" ).fill( "Password", "secret" ).click( "Sign in" )
} )

browse( ( page ) => {
	visitRoute( page, "admin.dashboard" )
	expect( page ).toSee( "Dashboard" )
}, { session : "admin" } )
```

See [Authentication](../../the-basics/testing-quick-start/browser-testing/authentication.md).

### 🔗 Module Route Links Fixed

`event.route( "name@module" )` built module route links without a slash between the module entry point and the route pattern, for example `bloglogin/3/` instead of `blog/login/3/`. Module links now always join them with a single slash.

## Release Notes

{% tabs %}
{% tab title="ColdBox" %}
### New Features

Browser testing for ColdBox applications (BoxLang), built on TestBox browser support and bx-playwright: annotate any `BaseTestCase` with `@browser`, `@browserProfile` or `@baseURL` and it gets TestBox's `browse()`, `this.playwright()`, `browserAvailable()` and browser matchers, while it still loads your application like any integration test. `BaseTestCase` adds the ColdBox helpers `routeURL()`, `visitRoute()` and `assertRouteIs()` for named routes, including module routes. Logged-in tests use bx-playwright saved sessions. Needs a TestBox release with annotation-driven browser support ([TestBox#222](https://github.com/Ortus-Solutions/TestBox/pull/222)) ([#708](https://github.com/ColdBox/coldbox-platform/pull/708))


### Bugs

`event.route( "name@module" )` built module route links without a slash between the module entry point and the route pattern ([#708](https://github.com/ColdBox/coldbox-platform/pull/708))
{% endtab %}
{% endtabs %}
