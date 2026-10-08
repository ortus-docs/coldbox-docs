---
description: >-
  Write a BrowserTestCase spec: annotations, browse(), the bx-playwright page
  API and the TestBox browser matchers.
icon: pen-to-square
---

# Writing Browser Tests

A browser spec is a BDD bundle that extends `coldbox.system.testing.BrowserTestCase`. You open pages with `browse()`, drive them with the [bx-playwright page API](https://bxplaywright.boxlang.io/browsing/), and verify them with `expect()` and the browser matchers.

## A Complete Spec

```javascript
/**
 * tests/specs/browser/ContactBrowserSpec.bx
 */
@appMapping( "/root" )
@baseURL( "http://127.0.0.1:8080" )
class extends="coldbox.system.testing.BrowserTestCase" {

	function beforeAll() {
		// Loads the virtual ColdBox application: keep the super call
		super.beforeAll()
	}

	function afterAll() {
		super.afterAll()
	}

	function run() {
		describe( "Contact page", () => {

			it( "renders the home page", () => {
				browse( ( page ) => {
					page.visit( "/" )
					expect( page ).toHaveTitle( "Welcome to ColdBox!" )
					expect( page ).toSee( "Welcome" )
					expect( page ).notToSee( "Exception" )
				} )
			} )

			it( "sends a message", () => {
				browse( ( page ) => {
					visitRoute( page, "contact" )

					page.fill( "Email", "luis@ortus.com" )
						.fill( "Message", "Hello from a browser test" )
						.click( "Send" )

					assertRouteIs( page, "contact.thanks" )
					expect( page ).toSee( "Thanks for your message" )
				} )
			} )

			it( "validates the form", () => {
				browse( ( page ) => {
					visitRoute( page, "contact" ).click( "Send" )

					expect( page.locator( ".invalid-feedback" ) ).toBeVisible()
					expect( page.locator( ".invalid-feedback" ) ).toHaveCount( 2 )
					expect( page.byLabel( "Email" ) ).toHaveValue( "" )
				} )
			} )

			it( "works on a phone", () => {
				browse(
					( page ) => {
						page.visit( "/" )
						expect( page.locator( "nav .navbar-toggler" ) ).toBeVisible()
					},
					{ viewport : { width : 390, height : 844 } }
				)
			} )

		} )
	}

}
```

`beforeAll()` and `afterAll()` work exactly like in any [integration test](../integration-testing/README.md): keep the `super` calls so the virtual application loads and unloads. You do not need a super call to close the browser: the inherited `closeBrowser()` method carries the `afterAll` annotation and runs after your own `afterAll()`. If your `afterAll()` throws or a spec calls `abort`, the browser stays open until the next test run that opens a browser, which closes it first. bx-playwright also closes anything left open when the module unloads or the JVM stops.

## Class Annotations

Every [BaseTestCase annotation](../integration-testing/test-annotations.md) works (`appMapping`, `webMapping`, `configMapping`, `coldboxAppKey`, `loadColdBox`, `unloadColdBox`), plus two browser annotations:

| Annotation | Description |
| --- | --- |
| `baseURL` | The URL of your running application. Relative visits such as `page.visit( "/login" )` resolve against it. Defaults to the `--web-server-url` of the BoxLang runner, then to the bx-playwright `baseURL` setting or `BX_PLAYWRIGHT_BASEURL` |
| `browserProfile` | bx-playwright profiles for the bundle browser, a list such as `ci,mobile`. Empty uses `BX_PLAYWRIGHT_PROFILE` or your module settings |

```javascript
@appMapping( "/root" )
@baseURL( "http://127.0.0.1:8080" )
@browserProfile( "firefox,dark" )
class extends="coldbox.system.testing.BrowserTestCase" {
	// ...
}
```

See [bx-playwright Profiles](https://bxplaywright.boxlang.io/profiles/) for the built-in browsers, devices, screens and modes.

## The Browser Methods

| Method | Description |
| --- | --- |
| `browse( callback, [options] )` | Runs the callback with fresh pages, one per declared argument, each in its own browser context (its own cookies and storage). Closes them when the callback ends. `options` are bx-playwright context options such as `viewport`, `locale` or `colorScheme`. Returns the callback result |
| `this.playwright()` | The bx-playwright manager of the bundle, created on first use with the `browserProfile` and `baseURL` annotations |
| `browserAvailable()` | `true` on BoxLang with bx-playwright installed. Handy for `skip` arguments |
| `routeURL( name, [params] )` | The path of a named route. See [Named Routes](named-routes.md) |
| `visitRoute( page, name, [params] )` | Visits a named route |
| `assertRouteIs( page, name, [params] )` | Asserts the page is on a named route |

The bundle shares **one browser**, started on first use and closed after the bundle, while every `browse()` call gets new, isolated pages. Tests never leak cookies or sessions into each other.

{% hint style="warning" %}
Call `this.playwright()`, not `playwright()`. An unqualified `playwright()` call resolves to the bx-playwright BIF, which returns a **new** manager that the bundle does not close.
{% endhint %}

### Several Users at Once

Declare one argument per page. Each page has its own session, so you can test interactions between users:

```javascript
it( "keeps every user in their own session", () => {
	browse( ( admin, guest ) => {
		visitRoute( admin, "login" ).fill( "Email", "admin@example.com" ).fill( "Password", "secret" ).click( "Sign in" )
		visitRoute( admin, "dashboard" )
		visitRoute( guest, "dashboard" )

		expect( admin ).toSee( "Admin Dashboard" )
		expect( guest ).toSee( "Please sign in" )
	} )
} )
```

### Skipping Without a Browser

The browser methods skip the running spec when bx-playwright is not available. To skip a whole suite up front, use `browserAvailable()`:

```javascript
describe(
	title = "Checkout",
	body  = () => {
		// ...
	},
	skip = !browserAvailable()
)
```

## The Browser Matchers

TestBox registers its browser matchers for every `BrowserTestCase` bundle. They delegate to the retrying bx-playwright assertions, so they **wait** until the condition is true or the assertion timeout expires (5 seconds by default). Every matcher has a `not` form, such as `notToSee()`, which waits too.

| Matcher | Target | Passes when |
| --- | --- | --- |
| `toHaveTitle( title )` | Page | The title is exactly `title`, or matches a regex from `page.regex()` |
| `toHaveURL( url )` | Page | The full URL is exactly `url`, or matches a regex |
| `toHavePath( path )` | Page | The path is exactly `path`, ignoring scheme, host, query string and hash |
| `toSee( text )` | Page or locator | The text, or a regex, is visible |
| `toHaveText( text )` | Locator | The text of the first element is `text` (whitespace normalized), or matches a regex |
| `toBeVisible()` | Locator | The first element is visible |
| `toBeHidden()` | Locator | The first element is hidden or not in the page |
| `toHaveCount( count )` | Locator | The locator matches exactly `count` elements |
| `toHaveValue( value )` | Locator | The input, textarea or select value is `value`, or matches a regex |

```javascript
expect( page ).toHavePath( "/dashboard" )
expect( page ).toSee( page.regex( "Welcome, \w+" ) )
expect( page.locator( ".alert-danger" ) ).toBeHidden()
expect( page.locator( "table tbody tr" ) ).toHaveCount( 10 )
```

The bx-playwright inline assertions, such as `page.assertSee()` or `page.assertPathIs()`, work too: their `Playwright.AssertionFailed` errors count as spec failures, not errors. See [TestBox Browser Matchers](https://testbox.ortusbooks.com/browser-testing/browser-matchers) for the matchers in depth and [bx-playwright Assertions](https://bxplaywright.boxlang.io/assertions/) for every inline assertion.

{% hint style="info" %}
Browser specs are not thread safe: do not use `asyncAll` in suites that browse.
{% endhint %}

## Flaky Specs and Retries

Real browsers are slower and less deterministic than virtual requests. TestBox can rerun a failing spec before reporting it:

```javascript
it( title = "uploads an avatar", retries = 2, body = () => {
	browse( ( page ) => {
		// ...
	} )
} )
```

You can also put a `retries` annotation on the bundle, or pass `--retries=N` to the BoxLang runner. The spec value wins over the bundle annotation, which wins over the runner option. Prefer fixing the cause first: the retrying matchers already wait for the page.

## See Also

* [Named Routes](named-routes.md)
* [Authentication](authentication.md)
* [bx-playwright Browsing](https://bxplaywright.boxlang.io/browsing/)
* [bx-playwright Page Objects](https://bxplaywright.boxlang.io/page-objects/)
