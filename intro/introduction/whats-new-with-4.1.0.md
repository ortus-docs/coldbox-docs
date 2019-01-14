# What's New With 4.1.0

ColdBox 4.1.0 is a minor release that addresses several issues and introduces some enhancements. You can see below the release notes.

## Release Notes

 Bugs

* \[[COLDBOX-434](https://ortussolutions.atlassian.net/browse/COLDBOX-434)\] - Controller loc.args is referenced and not existing in pre{Actions}
* \[[COLDBOX-435](https://ortussolutions.atlassian.net/browse/COLDBOX-435)\] - Module routing was not respecting package resolving within handlers
* \[[COLDBOX-441](https://ortussolutions.atlassian.net/browse/COLDBOX-441)\] - onError handler doesn't work
* \[[COLDBOX-454](https://ortussolutions.atlassian.net/browse/COLDBOX-454)\] - abstractFlashScope getKeys\(\) doesn't work

 Improvements

* \[[COLDBOX-425](https://ortussolutions.atlassian.net/browse/COLDBOX-425)\] - Modules that include applicationhelpers are incompat with modules.autoReload setting, add recognition
* \[[COLDBOX-440](https://ortussolutions.atlassian.net/browse/COLDBOX-440)\] - Add x-forwarded-proto checks on isSSL\(\) verifications to allow for proxy configuraitons
* \[[COLDBOX-444](https://ortussolutions.atlassian.net/browse/COLDBOX-444)\] - Better locking naming technique to avoid server-wide collisions
* \[[COLDBOX-455](https://ortussolutions.atlassian.net/browse/COLDBOX-455)\] - Ability to test ORM entities with base cases if loading ColdBox or using entity injection

 New Features

* \[[COLDBOX-430](https://ortussolutions.atlassian.net/browse/COLDBOX-430)\] - Update application templates to use new logo
* \[[COLDBOX-448](https://ortussolutions.atlassian.net/browse/COLDBOX-448)\] - Add session and application timeouts to testing harnesses
* \[[COLDBOX-449](https://ortussolutions.atlassian.net/browse/COLDBOX-449)\] - Create a new testing annotation to control if application should be teardown in integration tests
* \[[COLDBOX-457](https://ortussolutions.atlassian.net/browse/COLDBOX-457)\] - Create REST application template for ColdBox

