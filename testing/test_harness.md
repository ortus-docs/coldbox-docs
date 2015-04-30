# Test Harness

Every ColdBox application template comes with a nice test harness inside of a `tests` folder.

![](../images/testharness.png)

Here is a breakdown of what it contains:

* `Application.cfc` - A unique application file for your test harness. It should mimic exactly the one in your root application folder
* `resources` - Some basic testing resources or any of your own testing resources
* `results` - Where automated results are archived
* `runner.cfm` - The HTML runner for your test bundles
* `specs` - Where you will be writing your testing bundle specs for integration testing, unit testing and module testing.
* `test.xml` - Your ANT runner
