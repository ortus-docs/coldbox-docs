# What's New With 4.2.0

ColdBox 4.2.0 is a minor release that addresses several issues and introduces some enhancements.  You can see below the release notes.


## Release Notes
            

### Bugs

<ul>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-429'>COLDBOX-429</a>] -         Bundling Modules w/ module excludes does not respect excludes
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-453'>COLDBOX-453</a>] -         API docs have broken links
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-462'>COLDBOX-462</a>] -         adobe CF incompatibillty on restful template response object
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-463'>COLDBOX-463</a>] -         addAsset does not recognize urls that ends with say &quot;app.js?123&quot; as js, not css.
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-467'>COLDBOX-467</a>] -         fix for tomcat 8 not removing repeating slashes on path info
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-471'>COLDBOX-471</a>] -         HTML Helper&#39;s startForm() doesn&#39;t pick up if current request is HTTPS
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-474'>COLDBOX-474</a>] -         doctype default switch case was on the wrong type, thanks to Hector Cruz
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-475'>COLDBOX-475</a>] -         _counter does not increment in rendering query-based view collections
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-485'>COLDBOX-485</a>] -         Bean Populator not working correctly when ORM entity inherits from other
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-487'>COLDBOX-487</a>] -         Bean populator errors on null values in JSON string
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-490'>COLDBOX-490</a>] -          syntax and wrong argument type matching on exception bean object
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-497'>COLDBOX-497</a>] -         double forward slash generated in `buildLink`
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-509'>COLDBOX-509</a>] -         SES interceptor has poor query string parsing, updated to new algorithm
</li>
</ul>
            
### New Features
<ul>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-477'>COLDBOX-477</a>] -         Update testing docs for collaboration and update core for CommandBox development
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-483'>COLDBOX-483</a>] -         Add a test browser by default to the test harness
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-492'>COLDBOX-492</a>] -         Add a new &#39;route,querystring&#39; argument to the &#39;execute&#39; method to allow for SES route testing
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-493'>COLDBOX-493</a>] -         Added a &#39;memento&#39; argument to the &#39;populateModel&#39; method to allow for overriding of what struct to populate with
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-494'>COLDBOX-494</a>] -         Added json,xml,query additions to the populateModel() method to allow for more populations
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-498'>COLDBOX-498</a>] -         Update build process for DocBox and travis integrations
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-499'>COLDBOX-499</a>] -         Update and Cleanup of app templates
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-500'>COLDBOX-500</a>] -         Allow for applications with no ColdBox.cfc config, full convention mode
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-501'>COLDBOX-501</a>] -         new convenience method on testing request context to retrieve rendered content: getRenderedContent()
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-506'>COLDBOX-506</a>] -         Refactor app templates to their own repo
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-507'>COLDBOX-507</a>] -         New context method: getHTMLBaseURL() to get a http protocol sensitive request context base url
</li>
</ul>
        
<h2>        Improvement
</h2>
<ul>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-191'>COLDBOX-191</a>] -         Flag to turn view caching on/off
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-246'>COLDBOX-246</a>] -         Autowire remote proxies, changed to manual instead of automatic
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-348'>COLDBOX-348</a>] -         Remove requirement to have at least one handler
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-472'>COLDBOX-472</a>] -         Update the documentation URL in box.json
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-476'>COLDBOX-476</a>] -         Review duplication of error object in ExceptionBean.cfc
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-486'>COLDBOX-486</a>] -         Support for german characters for slugify
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-495'>COLDBOX-495</a>] -         Load internal system modules first rather than last in module hierarchy discovery
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-496'>COLDBOX-496</a>] -         Error detail, message empty on CF10 CF11
</li>
<li>[<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-503'>COLDBOX-503</a>] -         Replace StringBuffer with StringBuilder for performance
</li>
</ul>
                                        
