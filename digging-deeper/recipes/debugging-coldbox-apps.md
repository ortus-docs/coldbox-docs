# Debugging ColdBox Apps with CbDebugger

We have a special module just for helping you monitor and debug ColdBox applications. To install, fire up CommandBox and install it as a dependency in your application.

## CbDebugger Requirements

- Hibernate extension (on Lucee)
- ORM package (on ACF 2021)

```bash
install cbdebugger --saveDev
```

Remember that this module is for development so use the `--saveDev` flag. This will install the ColdBox Debugger module for you, which will attach a debugger to the end of the request and give you lots of flexibility. Please read the instructions here in order to spice it up as you see fit: [https://github.com/coldbox-modules/cbdebugger](https://github.com/coldbox-modules/cbdebugger)

![](../../.gitbook/assets/cachemonitor.jpg)

