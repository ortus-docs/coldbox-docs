# What's New With 4.2.0

ColdBox 4.2.0 is a minor release that addresses several issues and introduces some enhancements.  You can see below the release notes.


## Release Notes
            

### Bugs

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-429'>COLDBOX-429</a>] - Bundling Modules w/ module excludes does not respect excludes
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-453'>COLDBOX-453</a>] - API docs have broken links
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-462'>COLDBOX-462</a>] - adobe CF incompatibillty on restful template response object
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-463'>COLDBOX-463</a>] - addAsset does not recognize urls that ends with say &quot;app.js?123&quot; as js, not css.
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-467'>COLDBOX-467</a>] - fix for tomcat 8 not removing repeating slashes on path info
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-471'>COLDBOX-471</a>] - HTML Helper&#39;s startForm() doesn&#39;t pick up if current request is HTTPS
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-474'>COLDBOX-474</a>] - doctype default switch case was on the wrong type, thanks to Hector Cruz
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-475'>COLDBOX-475</a>] - _counter does not increment in rendering query-based view collections
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-485'>COLDBOX-485</a>] - Bean Populator not working correctly when ORM entity inherits from other
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-487'>COLDBOX-487</a>] - Bean populator errors on null values in JSON string
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-490'>COLDBOX-490</a>] - syntax and wrong argument type matching on exception bean object
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-497'>COLDBOX-497</a>] - double forward slash generated in `buildLink`
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-509'>COLDBOX-509</a>] - SES interceptor has poor query string parsing, updated to new algorithm
 
### New Features

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-477'>COLDBOX-477</a>] - Update testing docs for collaboration and update core for CommandBox development
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-483'>COLDBOX-483</a>] - Add a test browser by default to the test harness
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-492'>COLDBOX-492</a>] - Integration testing `execute` method has two new arguments: `route,querystring` to alow you to do SES route testing
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-493'>COLDBOX-493</a>] - Added a `memento` argument to the `populateModel` method to allow for overriding of what struct to populate with instead of the request collection
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-494'>COLDBOX-494</a>] - Added json,xml,query additions to the `populateModel()` method to allow for more populations from different types of data structures
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-498'>COLDBOX-498</a>] - Update build process for DocBox and travis integrations
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-499'>COLDBOX-499</a>] - Update and Cleanup of app templates
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-500'>COLDBOX-500</a>] - Allow for applications with no ColdBox.cfc config, full convention mode
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-501'>COLDBOX-501</a>] - new convenience method on testing request context to retrieve rendered content: getRenderedContent()
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-506'>COLDBOX-506</a>] - Refactor app templates to their own repo
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-507'>COLDBOX-507</a>] - New context method: getHTMLBaseURL() to get a http protocol sensitive request context base url

        
### Improvements

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-191'>COLDBOX-191</a>] - New setting directive `viewCaching` to turn view caching on/off
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-246'>COLDBOX-246</a>] - Autowire remote proxies, changed to manual instead of automatic due to ORM issues
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-348'>COLDBOX-348</a>] - Remove requirement to have at least one handler in your application for ColdBox to work.
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-472'>COLDBOX-472</a>] - Update the documentation URL in box.json
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-486'>COLDBOX-486</a>] - Support for german characters for slugify for HTML helper
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-495'>COLDBOX-495</a>] - Load internal system modules first rather than last in module hierarchy discovery
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-496'>COLDBOX-496</a>] - Error detail, message empty on CF10 CF11
* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-503'>COLDBOX-503</a>] - Replace StringBuffer with StringBuilder for performance improvements on later JDKs
              
