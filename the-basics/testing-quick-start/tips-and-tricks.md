---
description: Testing tips — use AppMapping-based paths, relocate(), querySim(), MockBox, and the cbDebugger module.
---

# Tips & Tricks

Here are some useful tips for you when doing testing with ColdBox Applications:

* If you are using relative paths in your application, you might encounter problems since the running application is different from the test application. Try to always use paths based on the application's `AppMapping`
* Always use `relocate()` for relocations so they can be mocked
* Leverage `querySim()` for query mocking
* Leverage [MockBox](https://testbox.ortusbooks.com/mocking/mockbox) for mocking and stubbing
* Integration tests are NOT the same as handler tests. Handler tests will just test the handler class in isolation, so it will be your job to mock everything around it.
* You can extend the `coldbox.system.testing.BaseModelTest` to test any domain object
* The [ColdBox source code testing folder](https://github.com/ColdBox/coldbox-platform/tree/master/tests) has over 5,000 tests, mocking scripts and more for you to learn from

## Debugging with cbDebugger

For development-time insight into requests, install the ColdBox Debugger module as a dev dependency:

```bash
box install cbdebugger --saveDev
```

It attaches a debug panel to the end of each request with timers, collections, WireBox info, and more. See the [cbdebugger module](https://github.com/coldbox-modules/cbdebugger) for configuration. Always use `--saveDev` — the debugger is a development tool, not for production.
