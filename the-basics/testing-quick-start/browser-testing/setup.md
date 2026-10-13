---
description: >-
  Install bx-playwright and Chromium, check the version requirements and run
  your ColdBox application where the browser can reach it.
icon: screwdriver-wrench
---

# Setup

Browser tests need three things: the bx-playwright module and a browser, a TestBox and ColdBox version with browser support, and your application running on a web server the browser can reach.

{% hint style="info" %}
Browser testing is **BoxLang only**. On CFML engines exclude your browser specs folder from the runner (see [Running on Several Engines](#running-on-several-engines)).
{% endhint %}

## Requirements

| Requirement | Version |
| --- | --- |
| BoxLang | 1.17 or later |
| Java | 21 or later |
| ColdBox | 8.3.0 or later (`BaseTestCase` route helpers) |
| TestBox | 7.2.0 or later, with annotation-driven browser support ([TestBox#222](https://github.com/Ortus-Solutions/TestBox/pull/222)) |
| bx-playwright | Latest |

```bash
box install coldbox@^8.3.0
box install testbox@^7.2.0 --saveDev
```

## Install bx-playwright and a Browser

bx-playwright ships a `bxplaywright` command line tool that downloads the Playwright driver, a small Node.js distribution and the browsers.

```bash
# The module and its CLI for the BoxLang OS runtime
install-bx-module bx-playwright

# Driver + Node.js + Chromium, then check everything
bxplaywright install
bxplaywright doctor
```

Need more browsers? `bxplaywright install firefox webkit`. On Linux CI machines add `--with-deps` to also install the operating system libraries the browsers need. Everything is stored in `~/.boxlang/playwright`, shared by every BoxLang runtime of the same user.

The module must also be loaded by the BoxLang runtime that **runs your tests**. If your tests run in a CommandBox BoxLang server (with the `commandbox-boxlang` module), install it in that server, for example from your `server.json`, the same way the ColdBox platform installs its BoxLang modules:

```json
{
    "app": { "cfengine": "boxlang@1" },
    "scripts": {
        "onServerInitialInstall": "install bx-playwright --noSave"
    }
}
```

See [bx-playwright: Getting Started](https://bxplaywright.boxlang.io/getting-started/) and the [CLI reference](https://bxplaywright.boxlang.io/cli/) for every option.

## Run Your Application

The browser needs a running application. Point your specs at it with the `baseURL` class annotation, and relative visits such as `page.visit( "/login" )` resolve against it.

{% tabs %}

{% tab title="CommandBox Server" %}

Start your application as usual and run the tests through the HTML runner of the same server, so the browser and your specs see the same application.

```bash
box server start
box testbox run
```

```javascript
@appMapping( "/root" )
@browser
@baseURL( "http://127.0.0.1:8080" )
class extends="coldbox.system.testing.BaseTestCase" {
	// ...
}
```

{% endtab %}

{% tab title="BoxLang MiniServer" %}

Serve your application with the BoxLang MiniServer and run the tests through TestBox's HTML runner on the same server. Copy `testbox/bx/tests/runner.bxm` to `tests/runner.bxm` if your app does not have a runner yet.

```bash
# The server runtime loads the modules your app and specs use
install-bx-module bx-playwright
install-bx-module bx-esapi
boxlang-miniserver --port 8080 --rewrites
```

Open `http://127.0.0.1:8080/tests/runner.bxm?directory=tests.specs.browser`, or run it from a terminal:

```bash
curl -s "http://127.0.0.1:8080/tests/runner.bxm?directory=tests.specs.browser&reporter=text"
```

```javascript
@browser
@baseURL( "http://127.0.0.1:8080" )
class extends="coldbox.system.testing.BaseTestCase" {
	// ...
}
```

{% endtab %}

{% endtabs %}

{% hint style="warning" %}
**Run `BaseTestCase` browser specs through a web runner.** TestBox's BoxLang CLI runner (`./testbox/run`) cannot run them yet: the spec loads your ColdBox application virtually, and ColdBox reads the `cgi` scope, which only exists in a web request. Use the HTML runner of the server that serves your application, as above. The `--web-server` options of the CLI runner work for plain TestBox browser specs that do not extend `BaseTestCase`.
{% endhint %}

{% hint style="info" %}
ColdBox builds links with `encodeForURL()`, which BoxLang provides through the **bx-esapi** module. If your pages fail with `Function [encodeForURL] not found`, install it in the runtime that serves your application: `install-bx-module bx-esapi`.
{% endhint %}

The base URL is resolved in this order:

1. The `baseURL` annotation of the spec class (or a class it extends)
2. The `--web-server-url` of the BoxLang CLI runner, for specs it can run
3. The bx-playwright `baseURL` setting or the `BX_PLAYWRIGHT_BASEURL` environment variable

## Organize Your Specs

Keep browser specs in their own folder so you can run, exclude or parallelize them separately:

```
tests/
  specs/
    browser/        <- browser bundles (.bx, BaseTestCase + @browser)
    integration/
    unit/
```

## Running on Several Engines

If the same test suite also runs on Lucee or Adobe ColdFusion, exclude the browser folder there. This is what the ColdBox platform does in its own `tests/runner.cfm`:

```javascript
// Browser specs are BoxLang classes: skip them on the other engines
if ( !structKeyExists( server, "boxlang" ) ) {
	url.directoryExcludes = listAppend( url.directoryExcludes, "/browser" );
}
```

On BoxLang without bx-playwright, nothing needs excluding: every spec that calls `browse()` is skipped with the reason (`bx-playwright is not installed: install-bx-module bx-playwright`).

## See Also

* [Writing Browser Tests](writing-browser-tests.md)
* [Continuous Integration](continuous-integration.md)
* [Test Harness](../test-harness.md)
* [bx-playwright Configuration](https://bxplaywright.boxlang.io/configuration/)
