# What's New With 4.2.0

ColdBox 4.2.0 is a minor release that addresses several issues and introduces some enhancements. You can see below the release notes.

## Release Notes

### Bugs

* \[[COLDBOX-429](https://ortussolutions.atlassian.net/browse/COLDBOX-429)\] - Bundling Modules w/ module excludes does not respect excludes
* \[[COLDBOX-453](https://ortussolutions.atlassian.net/browse/COLDBOX-453)\] - API docs have broken links
* \[[COLDBOX-462](https://ortussolutions.atlassian.net/browse/COLDBOX-462)\] - adobe CF incompatibillty on restful template response object
* \[[COLDBOX-463](https://ortussolutions.atlassian.net/browse/COLDBOX-463)\] - addAsset does not recognize urls that ends with say "app.js?123" as js, not css.
* \[[COLDBOX-467](https://ortussolutions.atlassian.net/browse/COLDBOX-467)\] - fix for tomcat 8 not removing repeating slashes on path info
* \[[COLDBOX-471](https://ortussolutions.atlassian.net/browse/COLDBOX-471)\] - HTML Helper's startForm\(\) doesn't pick up if current request is HTTPS
* \[[COLDBOX-474](https://ortussolutions.atlassian.net/browse/COLDBOX-474)\] - doctype default switch case was on the wrong type, thanks to Hector Cruz
* \[[COLDBOX-475](https://ortussolutions.atlassian.net/browse/COLDBOX-475)\] - \_counter does not increment in rendering query-based view collections
* \[[COLDBOX-485](https://ortussolutions.atlassian.net/browse/COLDBOX-485)\] - Bean Populator not working correctly when ORM entity inherits from other
* \[[COLDBOX-487](https://ortussolutions.atlassian.net/browse/COLDBOX-487)\] - Bean populator errors on null values in JSON string
* \[[COLDBOX-490](https://ortussolutions.atlassian.net/browse/COLDBOX-490)\] - syntax and wrong argument type matching on exception bean object
* \[[COLDBOX-497](https://ortussolutions.atlassian.net/browse/COLDBOX-497)\] - double forward slash generated in `buildLink`
* \[[COLDBOX-509](https://ortussolutions.atlassian.net/browse/COLDBOX-509)\] - SES interceptor has poor query string parsing, updated to new algorithm

### New Features

* \[[COLDBOX-477](https://ortussolutions.atlassian.net/browse/COLDBOX-477)\] - Update testing docs for collaboration and update core for CommandBox development
* \[[COLDBOX-483](https://ortussolutions.atlassian.net/browse/COLDBOX-483)\] - Add a test browser by default to the test harness
* \[[COLDBOX-492](https://ortussolutions.atlassian.net/browse/COLDBOX-492)\] - Integration testing `execute` method has two new arguments: `route,querystring` to alow you to do SES route testing
* \[[COLDBOX-493](https://ortussolutions.atlassian.net/browse/COLDBOX-493)\] - Added a `memento` argument to the `populateModel` method to allow for overriding of what struct to populate with instead of the request collection
* \[[COLDBOX-494](https://ortussolutions.atlassian.net/browse/COLDBOX-494)\] - Added json,xml,query additions to the `populateModel()` method to allow for more populations from different types of data structures
* \[[COLDBOX-498](https://ortussolutions.atlassian.net/browse/COLDBOX-498)\] - Update build process for DocBox and travis integrations
* \[[COLDBOX-499](https://ortussolutions.atlassian.net/browse/COLDBOX-499)\] - Update and Cleanup of app templates
* \[[COLDBOX-500](https://ortussolutions.atlassian.net/browse/COLDBOX-500)\] - Allow for applications with no `ColdBox.cfc` config, full convention mode
* \[[COLDBOX-501](https://ortussolutions.atlassian.net/browse/COLDBOX-501)\] - new convenience method on testing request context to retrieve rendered content: `getRenderedContent()`
* \[[COLDBOX-506](https://ortussolutions.atlassian.net/browse/COLDBOX-506)\] - Refactor app templates to their own repositories
* \[[COLDBOX-507](https://ortussolutions.atlassian.net/browse/COLDBOX-507)\] - New context method: `getHTMLBaseURL()` to get a http protocol sensitive request context base url

### Improvements

* \[[COLDBOX-191](https://ortussolutions.atlassian.net/browse/COLDBOX-191)\] - New setting directive `viewCaching` to turn view caching on/off
* \[[COLDBOX-246](https://ortussolutions.atlassian.net/browse/COLDBOX-246)\] - Autowire remote proxies, changed to manual instead of automatic due to ORM issues
* \[[COLDBOX-348](https://ortussolutions.atlassian.net/browse/COLDBOX-348)\] - Remove requirement to have at least one handler in your application for ColdBox to work.
* \[[COLDBOX-472](https://ortussolutions.atlassian.net/browse/COLDBOX-472)\] - Update the documentation URL in box.json
* \[[COLDBOX-486](https://ortussolutions.atlassian.net/browse/COLDBOX-486)\] - Support for german characters for slugify for HTML helper
* \[[COLDBOX-495](https://ortussolutions.atlassian.net/browse/COLDBOX-495)\] - Load internal system modules first rather than last in module hierarchy discovery
* \[[COLDBOX-496](https://ortussolutions.atlassian.net/browse/COLDBOX-496)\] - Error detail, message empty on CF10 CF11
* \[[COLDBOX-503](https://ortussolutions.atlassian.net/browse/COLDBOX-503)\] - Replace StringBuffer with StringBuilder for performance improvements on later JDKs

