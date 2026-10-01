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
| ColdBox | 8.3.0 or later (`BrowserTestCase` and the `BrowserTesting` module) |
| TestBox | 7.2.0 or later (`testbox.system.browser` support and matchers) |
| bx-playwright | Latest |

```bash
box install coldbox@^8.3.0
box install testbox@^7.2.0 --saveDev
```

## Install bx-playwright and a Browser

bx-playwright ships a `bxPlaywright` command line tool that downloads the Playwright driver, a small Node.js distribution and the browsers.

```bash
# The module and its CLI for the BoxLang OS runtime
install-bx-module bx-playwright

# Driver + Node.js + Chromium, then check everything
bxPlaywright install
bxPlaywright doctor
```

Need more browsers? `bxPlaywright install firefox webkit`. On Linux CI machines add `--with-deps` to also install the operating system libraries the browsers need. Everything is stored in `~/.boxlang/playwright`, shared by every BoxLang runtime of the same user.

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
class extends="coldbox.system.testing.BrowserTestCase" appMapping="/root" baseURL="http://127.0.0.1:8080" {
	// ...
}
```

{% endtab %}

{% tab title="BoxLang Runner" %}

TestBox's BoxLang runner can start the web server for you, wait until it answers, run the tests and stop it afterwards:

```bash
./testbox/run --directory=tests.specs.browser \
    --web-server="boxlang-miniserver --port 8080" \
    --web-server-url=http://localhost:8080
```

| Option | Default | Description |
| --- | --- | --- |
| `--web-server` | | Shell command that starts the web server before the tests |
| `--web-server-url` | `http://localhost:8080` | URL polled until it answers with a status below 500 |
| `--web-server-timeout` | `60` | Seconds to wait for the server; the runner exits with code `1` when it does not answer |

The `--web-server-url` also becomes the **default `baseURL`** of every browser bundle, so you can leave the annotation out.

`BrowserTestCase` still loads your ColdBox application virtually, so running it from the CLI needs the `bx-web-support` module in the BoxLang OS runtime (`install-bx-module bx-web-support`). See the [BoxLang CLI Runner](https://testbox.ortusbooks.com/getting-started/running-tests/boxlang-cli-runner) guide.

{% endtab %}

{% endtabs %}

The base URL is resolved in this order:

1. The `baseURL` annotation of the spec class (or a class it extends)
2. The `--web-server-url` of the BoxLang runner
3. The bx-playwright `baseURL` setting or the `BX_PLAYWRIGHT_BASEURL` environment variable

## Run the Application in the Testing Environment

The [`loginAs()` and `logout()` helpers](authentication.md) only work when the application under test runs in the ColdBox `testing` environment. The simplest way is the `ENVIRONMENT` environment variable, which ColdBox reads at startup when your configuration has no `detectEnvironment()` method:

```bash
export ENVIRONMENT=testing
export BROWSER_TESTING_TOKEN=$( openssl rand -hex 32 )
box server start
```

You can also map a host name to the environment with the `environments` directive of your ColdBox class, for example `testing : "^127\.0\.0\.1"`.

## Organize Your Specs

Keep browser specs in their own folder so you can run, exclude or parallelize them separately:

```
tests/
  specs/
    browser/        <- BrowserTestCase bundles (.bx)
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

On BoxLang without bx-playwright, nothing needs excluding: every spec that calls `browse()`, `visitRoute()`, `loginAs()` or `logout()` is skipped with the reason (`bx-playwright is not installed: install-bx-module bx-playwright`).

## See Also

* [Writing Browser Tests](writing-browser-tests.md)
* [Continuous Integration](continuous-integration.md)
* [Test Harness](../test-harness.md)
* [bx-playwright Configuration](https://bxplaywright.boxlang.io/configuration/)
