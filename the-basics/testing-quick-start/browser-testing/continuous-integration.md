---
description: >-
  Run ColdBox browser tests on GitHub Actions: start the application, install
  Chromium with its system libraries, run the specs and upload the artifacts.
icon: github
---

# Continuous Integration

A browser test job does four things: install BoxLang, bx-playwright and a browser, start your application in the `testing` environment, run the specs with the `ci` profile, and upload the screenshots, traces and videos of failed specs.

## GitHub Actions

This workflow runs a ColdBox application on a CommandBox BoxLang server and runs its tests through the HTML runner with `box testbox run`.

```yaml
name: Browser Tests

on: [ push, pull_request ]

jobs:
  browser-tests:
    runs-on: ubuntu-latest
    env:
      # The application detects its environment from this variable
      ENVIRONMENT: testing
      # Headless, keep screenshots, traces and videos of failures
      BX_PLAYWRIGHT_PROFILE: ci
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "21"

      # BoxLang OS runtime + bx-playwright, for the bxPlaywright CLI
      - uses: ortus-boxlang/setup-boxlang@main
        with:
          version: latest
          modules: bx-playwright

      - uses: Ortus-Solutions/setup-commandbox@v2.0.1
        with:
          install: commandbox-boxlang

      - name: Install dependencies
        run: box install

      # Driver, Node.js and browsers: one download per bx-playwright version
      - name: Read the bx-playwright version
        id: pw
        run: echo "version=$( jq -r .version ~/.boxlang/modules/bx-playwright/box.json )" >> "$GITHUB_OUTPUT"

      - uses: actions/cache@v4
        with:
          path: ~/.boxlang/playwright
          key: playwright-${{ runner.os }}-${{ steps.pw.outputs.version }}

      # --with-deps installs the system libraries, which are not cached
      - name: Install Chromium
        run: |
          export PATH="$HOME/.boxlang/bin:$PATH"
          bxPlaywright install chromium --with-deps
          bxPlaywright doctor

      - name: Start the application
        run: |
          box server start --noSaveSettings
          curl --silent --show-error --fail --retry 10 --retry-delay 3 --retry-all-errors http://127.0.0.1:8080 > /dev/null

      - name: Run the tests
        run: box testbox run

      - name: Upload failure artifacts
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: browser-test-artifacts
          path: |
            ~/.boxlang/playwright/artifacts
            tests/results
          if-no-files-found: ignore

      - name: Server log on failure
        if: failure()
        run: box server log
```

Adapt it to your project:

* The `server.json` of the application must install bx-playwright in the server (`"onServerInitialInstall": "install bx-playwright --noSave"`) and listen on the port your specs use as `baseURL`, here `8080`. See [Setup](setup.md).
* `box testbox run` needs the runner URL in your `box.json` (`testbox.runner`), see the [Testing quick start](../README.md#commandbox-runner).
* The server inherits `ENVIRONMENT` and `BX_PLAYWRIGHT_PROFILE` from the job, and so do the specs running inside it.
* Store the passwords of your test users as CI secrets, and read them in your saved session setup. See [Authentication](authentication.md#test-users-and-passwords).
* Open a downloaded trace locally with `bxPlaywright show-trace trace.zip`.


## With the BoxLang Runner

If your tests run with TestBox's BoxLang runner instead, let it start and stop the web server. Replace the last steps with:

```yaml
      - name: Run the browser tests
        run: |
          ./testbox/run --directory=tests.specs.browser \
            --web-server="boxlang-miniserver --port 8080" \
            --web-server-url=http://127.0.0.1:8080 \
            --retries=1
```

The runner waits until the URL answers (60 seconds by default, `--web-server-timeout`), uses it as the default `baseURL` of your bundles, stops the server after the tests, and exits with code `1` if the server never answers.

## Tips

* **Cache `~/.boxlang/playwright`** between runs, as above, to skip the driver and browser downloads.
* **Run browser specs in their own job** so a slow or flaky browser run does not hold back your fast unit and integration tests.
* **Publish JUnit reports** with an action such as `mikepenz/action-junit-report`: TestBox writes each attachment as an `[[ATTACHMENT|path]]` line in the `<system-out>` of the spec.
* **Retry sparingly**: `--retries=1` hides a timing glitch, but a spec that needs retries usually needs a better wait or selector.

## See Also

* [Artifacts & Debugging](artifacts-and-debugging.md)
* [Troubleshooting](troubleshooting.md)
* [bx-playwright Testing: CI](https://bxplaywright.boxlang.io/testing/)
* [setup-boxlang action](https://github.com/ortus-boxlang/setup-boxlang)
