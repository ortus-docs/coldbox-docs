---
description: Upcoming release
---

# What's New With 8.3.0

ColdBox 8.3.0 is a feature release that brings **real-browser testing** to ColdBox applications on BoxLang: a new `BrowserTestCase` base class built on TestBox browser support and the bx-playwright module, named route helpers, and test-only login and logout endpoints with a locked-down security model. It also fixes module named route links.

## Major Highlights

### 🌐 Browser Testing With BrowserTestCase (BoxLang)

{% hint style="warning" %}
🚀 **BoxLang Exclusive**: browser testing requires **BoxLang**, **TestBox 7.2.0+** and the **bx-playwright** module. `BrowserTestCase` is a BoxLang class: on CFML engines exclude your browser specs folder from the runner. On BoxLang without bx-playwright, browser specs are skipped.
{% endhint %}

`coldbox.system.testing.BrowserTestCase` extends `BaseTestCase`, so it loads your application virtually like any integration test and knows your routes and settings, while it drives a real Chromium, Firefox or WebKit browser against your **running** application:

```javascript
@appMapping( "/root" )
@baseURL( "http://127.0.0.1:8080" )
class extends="coldbox.system.testing.BrowserTestCase" {

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

* **`browse()`**, **`this.playwright()`** and **`browserAvailable()`** from TestBox browser support: one browser per bundle, fresh isolated pages per call, closed automatically after the bundle.
* The **`baseURL`** and **`browserProfile`** class annotations.
* The TestBox **browser matchers**: `toHaveTitle()`, `toHaveURL()`, `toHavePath()`, `toSee()`, `toHaveText()`, `toBeVisible()`, `toBeHidden()`, `toHaveCount()` and `toHaveValue()`, all retrying and all with `not` forms.
* Screenshots, traces and videos of failed specs **attached** to the spec in your reports.

See the [Browser Testing](../../the-basics/testing-quick-start/browser-testing/README.md) guide.

### 🧭 Named Route Helpers

Browser specs build URLs from your router instead of hard-coding them:

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

`coldbox.system.testing.BrowserTestCase` (BoxLang): browser tests for ColdBox applications built on TestBox browser support and bx-playwright, with `browse()`, `this.playwright()`, `browserAvailable()`, the `browserProfile` and `baseURL` annotations, the TestBox browser matchers, and the ColdBox helpers `routeURL()`, `visitRoute()` and `assertRouteIs()` ([#708](https://github.com/ColdBox/coldbox-platform/pull/708))


### Bugs

`event.route( "name@module" )` built module route links without a slash between the module entry point and the route pattern ([#708](https://github.com/ColdBox/coldbox-platform/pull/708))
{% endtab %}
{% endtabs %}
