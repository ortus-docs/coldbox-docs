# What's New With 5.3.0

ColdBox 5.3.0 is a minor version update with lots of fixes, improvements, performance enhancements and some nice new features.  Below are the major areas of improvement and the full release notes. To update your ColdBox installation just leverage CommandBox: `update coldbox`

## ColdBox

### Bugs

* \[[COLDBOX-741](https://ortussolutions.atlassian.net/browse/COLDBOX-741)\] - Defining UDF as **COLDBOX\_FAIL\_FAST** no longer returns false
* \[[COLDBOX-750](https://ortussolutions.atlassian.net/browse/COLDBOX-750)\] - Fixed typo\(s\) in call of function prePend\(\) on routing
* \[[COLDBOX-752](https://ortussolutions.atlassian.net/browse/COLDBOX-752)\] - base url not eliminating double slashes
* \[[COLDBOX-756](https://ortussolutions.atlassian.net/browse/COLDBOX-756)\] - Setter for valid extensions is setting the wrong variable
* \[[COLDBOX-759](https://ortussolutions.atlassian.net/browse/COLDBOX-759)\] - Cleanup the with closure on group routing to avoid leakage

### New Features

* \[[COLDBOX-753](https://ortussolutions.atlassian.net/browse/COLDBOX-753)\] - Add new flag to router: `multiDomainDiscovery` that can be turned on/off

### Improvements

* \[[COLDBOX-742](https://ortussolutions.atlassian.net/browse/COLDBOX-742)\] - Add trimming to asset loader to avoid spaces on links
* \[[COLDBOX-744](https://ortussolutions.atlassian.net/browse/COLDBOX-744)\] - InterceptorState + EventPool uses **syncrhonizedMap** that has locking issues: refactor to avoid these issues
* \[[COLDBOX-754](https://ortussolutions.atlassian.net/browse/COLDBOX-754)\] - set initiated bit for ColdBox after modules are activated
* \[[COLDBOX-760](https://ortussolutions.atlassian.net/browse/COLDBOX-760)\] - Performance improvements for interception registrations and discovery

## LogBox

### Improvements

* \[[LOGBOX-32](https://ortussolutions.atlassian.net/browse/LOGBOX-32)\] - Add test and fix for adding a LogBox category after the fact

## WireBox

### Improvements

* \[[WIREBOX-79](https://ortussolutions.atlassian.net/browse/WIREBOX-79)\] - Account for leading slashes in `mapDirectory()`
* \[[WIREBOX-80](https://ortussolutions.atlassian.net/browse/WIREBOX-80)\] - Throw a nicer DSL error if ColdBox is not linked
* \[[WIREBOX-81](https://ortussolutions.atlassian.net/browse/WIREBOX-81)\] - Performance improvements for the construction of transients

