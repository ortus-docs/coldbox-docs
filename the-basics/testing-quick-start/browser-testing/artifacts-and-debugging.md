---
description: >-
  Keep screenshots, traces and videos of failed browser specs, attach your own
  files, open traces and debug specs in a visible, slowed down browser.
icon: camera
---

# Artifacts & Debugging

A failing browser spec is only as useful as what it leaves behind. bx-playwright can record **screenshots**, **traces** and **videos**, and TestBox attaches the ones it keeps to the failed spec, so they show up in your reports and CI artifacts.

## Keep Artifacts of Failed Specs

Artifacts are **off** by default. Turn them on with a bx-playwright profile, for example the built-in `ci` profile, which runs headless and keeps a screenshot, a trace and a video of every failure:

```javascript
@appMapping( "/root" )
@browserProfile( "ci" )
class extends="coldbox.system.testing.BrowserTestCase" {
	// ...
}
```

Or, without touching your specs, choose the profile with an environment variable. It applies to every bundle without a `browserProfile` annotation:

```bash
export BX_PLAYWRIGHT_PROFILE=ci
```

When the `browse()` callback throws, its browser contexts close as **failed**, so the artifact policies keep their files, and every kept file is attached to the running spec with its type: `screenshot`, `trace` or `video`. The original exception is rethrown unchanged, so the spec still fails with its real message.

| Policy | Keeps |
| --- | --- |
| `off` | Nothing |
| `on` | Always |
| `only-on-failure` / `retain-on-failure` | Only when the context closes as failed |

You can set the policies yourself in the bx-playwright `artifacts` setting (`{ directory, screenshot, trace, video }`) or in a custom profile. Files go to `~/.boxlang/playwright/artifacts` unless you set `artifacts.directory`. See [bx-playwright Testing](https://bxplaywright.boxlang.io/testing/) and [Profiles](https://bxplaywright.boxlang.io/profiles/).

## Where Attachments Show Up

| Report or output | Attachments |
| --- | --- |
| JSON report | The `attachments` array of each spec |
| Simple (HTML) report | Links under the spec |
| JUnit and ANT JUnit reports | A `<system-out>` with one `[[ATTACHMENT\|path]]` line per file |
| Text, console and stream output | Listed under each failed spec |

## Attach Your Own Files

`attach( path, [type], [name] )` attaches any file to the running spec, passed or failed. Call it from a spec body or a `beforeEach()`, `afterEach()` or `aroundEach()`:

```javascript
it( "renders the invoice", () => {
	browse( ( page ) => {
		visitRoute( page, "invoices.show", { id : 1001 } )
		var file = expandPath( "/tests/results/invoice-1001.png" )
		page.screenshot( file )
		attach( file, "screenshot", "Invoice 1001" )
		expect( page ).toSee( "Total" )
	} )
} )
```

## Open a Trace

A trace records every action, network request, console message and DOM snapshot of a context. Open it in the Playwright trace viewer:

```bash
bxPlaywright show-trace path/to/trace.zip
```

You can step through the spec action by action and see the page before and after each one: usually the fastest way to understand a failure that only happens in CI.

## Debug a Spec

Run the bundle with the `debug` profile to watch the browser: it opens a **visible** browser, slows every action down by 250 milliseconds and records every artifact.

```javascript
@appMapping( "/root" )
@browserProfile( "debug" )
class extends="coldbox.system.testing.BrowserTestCase" {
	// ...
}
```

Other helpers:

* `@browserProfile( "headed" )` or `BX_PLAYWRIGHT_HEADLESS=false`: a visible browser at full speed.
* `page.snapshot()` prints the accessibility tree of the page, handy to find the right selector.
* `bxPlaywright codegen http://127.0.0.1:8080` records your clicks as BoxLang code you can paste into a spec.
* `debug( page.url() )` or `debug( page.content() )` adds values to the TestBox debug output.
* Focus a single spec with `fit()` or `fdescribe()` while you work on it.

## Rerun Only What Failed

The BoxLang runner remembers failures. `--failed` reruns only the bundles and specs that failed or errored in the last run:

```bash
./testbox/run --directory=tests.specs.browser
./testbox/run --directory=tests.specs.browser --failed
```

## See Also

* [Continuous Integration](continuous-integration.md)
* [Troubleshooting](troubleshooting.md)
* [bx-playwright CLI](https://bxplaywright.boxlang.io/cli/)
* [TestBox attachments](https://testbox.ortusbooks.com/browser-testing/attachments)
