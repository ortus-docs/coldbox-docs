---
name: commandbox-testing
description: "Use this skill for CommandBox TestBox integration: testbox run command, running tests from CLI, configuring runner URL in box.json, multiple output formats (json/antjunit), test watcher (testbox watch), CI integration, code coverage with FusionReactor, test labels and suites, and box.json testbox configuration."
---

# CommandBox TestBox Integration

## Overview

CommandBox has built-in integration with [TestBox](https://www.ortussolutions.com/products/testbox) — the BDD/TDD testing framework for CFML/BoxLang. Run your test suite from the CLI and integrate with CI pipelines.

```bash
# See all testbox commands
testbox help
```

---

## Requirements

- A running server (CommandBox or external)
- TestBox installed in your project
- A test runner file (default: `/tests/runner.cfm`)

```bash
# Install TestBox
install testbox --saveDev

# Start your server
server start

# Run tests
testbox run
```

---

## `testbox run`

```bash
# Run with default runner URL from box.json
testbox run

# Specify runner URL explicitly
testbox run "http://localhost:8080/tests/runner.cfm"

# Use relative path (CommandBox resolves host/port from server)
testbox run "/tests/runner.cfm"

# Verbose output (default)
testbox run --verbose

# Minimal output
testbox run --noVerbose

# Run specific test bundles
testbox run bundles=tests.unit.MyTest

# Run with labels
testbox run labels=unit
testbox run labels=unit,integration

# Run specific suites
testbox run testSuites=MySuite

# Run specific specs
testbox run testSpecs=itShouldDoSomething

# Multiple output formats
testbox run outputformats=json,antjunit,simple

# Output formats to file
testbox run outputformats=json,antjunit outputFile=build/test-results

# Additional URL options
testbox run options:opt1=value1 options:opt2=value2
```

### Example Output

```
Executing tests via http://127.0.0.1:8080/tests/runner.cfm...
TestBox v5.0.0
---------------------------------------------------------------------------
| Passed  | Failed  | Errored | Skipped | Time    | Bundles | Suites  | Specs   |
---------------------------------------------------------------------------
| 42      | 0       | 0       | 2       | 320 ms  | 3       | 8       | 44      |
---------------------------------------------------------------------------
```

---

## Configure Runner in `box.json`

Set the runner URL once so `testbox run` works without arguments:

```bash
# Set absolute URL
package set testbox.runner="http://localhost:8080/tests/runner.cfm"

# Set relative URL (auto-resolves from server settings)
package set testbox.runner="/tests/runner.cfm"

# Now just run
testbox run
```

---

## Full `box.json` TestBox Configuration

```json
{
    "testbox": {
        "runner": "http://localhost:8080/tests/runner.cfm",
        "verbose": false,
        "labels": "unit,integration",
        "testSuites": "MySuite",
        "testSpecs": "",
        "bundles": "",
        "recurse": true,
        "reporter": "json",
        "outputformats": "json,antjunit",
        "outputFile": "build/test-results",
        "watchDelay": 1000,
        "watchPaths": "/models/**.cfc,/handlers/**.cfc",
        "options": {
            "opt1": "value1"
        }
    }
}
```

---

## Test Watcher

`testbox watch` monitors files and re-runs tests on any change:

```bash
# Setup requirement: runner configured in box.json
package set testbox.runner=http://localhost:8080/tests/runner.cfm
server start

# Watch all files
testbox watch

# Watch specific pattern
testbox watch **.cfc
testbox watch /models/**.cfc,/handlers/**.cfc

# Using box.json watchPaths
package set testbox.watchPaths=/models/**.cfc
testbox watch
```

Watcher options in `box.json`:

```bash
package set testbox.watchDelay=500        # ms between checks (default: 1000)
package set testbox.verbose=false         # reduce output
package set testbox.labels=unit           # only run labeled tests
package set testbox.watchPaths=/models/**.cfc
```

Stop the watcher with `Ctrl+C`.

---

## CI Integration (GitHub Actions)

```yaml
# .github/workflows/tests.yml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Setup CommandBox
        uses: Ortus-Solutions/setup-commandbox@v2.0.0

      - name: Install Dependencies
        run: box install

      - name: Start Server
        run: box server start --noOpenBrowser

      - name: Run Tests
        run: box testbox run --noVerbose outputformats=antjunit outputFile=build/test-results

      - name: Publish Test Results
        uses: EnricoMi/publish-unit-test-result-action@v2
        if: always()
        with:
          files: "build/test-results*.xml"
```

---

## Multiple Output Formats

| Format | Description | File Extension |
|--------|-------------|----------------|
| `simple` | Text summary (default CLI output) | `.txt` |
| `json` | Full JSON test report | `.json` |
| `antjunit` | JUnit-compatible XML (for CI tools) | `.xml` |
| `mintext` | Minimal text output | `.txt` |
| `dot` | Dot-notation summary | `.txt` |

```bash
# Generate all formats
testbox run outputformats=json,antjunit,simple outputFile=build/results

# Files created: build/results.json, build/results.xml, build/results.txt
```

---

## Code Coverage

When running on a server with FusionReactor installed and TestBox 2.9+:

```bash
# Code coverage appears automatically in the output
testbox run

# Output includes:
# Code Coverage: 73% (450 LOC tracked)
```

---

## Common Patterns

### Run tests before server start (recipe)

```bash
# test-and-start.boxr
install
server start
testbox run || exit 1
```

```bash
recipe test-and-start.boxr
```

### Run tests in `box.json` scripts

```json
{
    "scripts": {
        "test": "testbox run",
        "test:watch": "testbox watch",
        "prePublish": "testbox run"
    }
}
```

```bash
run-script test
run-script test:watch
```

### Task Runner Integration

```javascript
// task.cfc
component {

    function test() {
        var exitCode = command( "testbox run" )
            .params( verbose=false )
            .run( returnExitCode=true );

        if ( exitCode != 0 ) {
            error( "Tests failed! Aborting build." );
        }

        print.greenLine( "All tests passed!" );
    }

}
```

```bash
task run test
```