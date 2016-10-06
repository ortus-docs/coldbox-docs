# What's New With 4.3.0

ColdBox 4.3.0 is a minor release that addresses several issues and introduces some enhancements. You can see below the release notes.

## Release Notes

### Bugs

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-479'>COLDBOX-479</a>] - onInvalidHTTPMethod not firing with SES Interceptor

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-512'>COLDBOX-512</a>] - ColboxProxy.cfc has reference to method tracer - which no longer exists in that component.

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-515'>COLDBOX-515</a>] - Exception handling broken for customErrorTemplate when application relative path used

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-517'>COLDBOX-517</a>] - CF mapping&#39;s for modules don&#39;t get created for proxy requests

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-520'>COLDBOX-520</a>] - Only remove the `scriptname` from the `pathinfo` if it is the first element in the `pathinfo`

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-521'>COLDBOX-521</a>] - afterAspectsLoad was missing from core

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-527'>COLDBOX-527</a>] - bug report view is not thread safe

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-528'>COLDBOX-528</a>] - Coldbox Event Cache discards the Content-Type when caching non renderdata results

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-529'>COLDBOX-529</a>] - Object in RC or PRC causes async interceptors to fail

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-534'>COLDBOX-534</a>] - getHTMLBaseURL returns with a double slash at the end,

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-537'>COLDBOX-537</a>] - ColdBox Cache Flash not discovering session/cookie as app has not loaded first

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-539'>COLDBOX-539</a>] - Private event actions are no longer executable

### New Features

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-371'>COLDBOX-371</a>] - Convention to override a module&#39;s settings in an application or parent module

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-505'>COLDBOX-505</a>] - New coldbox directive: invalidHTTPMethodHandler that will fire globally if an action is called with an invalid HTTP Method

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-511'>COLDBOX-511</a>] - New action annotation &quot;allowedMethods&quot; so you can allow inline annoation for allowed HTTP Verbs thanks to Nic Tunney

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-522'>COLDBOX-522</a>] - Add rc and prc available directly to custom error templates

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-525'>COLDBOX-525</a>] - HTTP Method Spoofing for Forms

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-530'>COLDBOX-530</a>] - Add &#39;modules_app&#39; as a default external location instead of manually adding it.

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-540'>COLDBOX-540</a>] - Interception points get reference to rc and prc now.

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-541'>COLDBOX-541</a>] - invokerasync was not passing the buffer

 ### Improvements

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-502'>COLDBOX-502</a>] - RequestBuffers now leverage string builders.

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-513'>COLDBOX-513</a>] - Calling processState from within an interceptor clears buffer

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-531'>COLDBOX-531</a>] - execute() in BaseTestCase now uses the default event (defined in config/ColdBox.cfc) if / is passed in as the route

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-532'>COLDBOX-532</a>] - event.getHTTPContent() doesn&#39;t work on binary encoding

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-536'>COLDBOX-536</a>] - Rendered output has ALWAYS a precedent empty line, delete it!

* [<a href='https://ortussolutions.atlassian.net/browse/COLDBOX-538'>COLDBOX-538</a>] - Alises in module models aren&#39;t picked up



