# What's New With 5.1.0

ColdBox 5.1.0 is a minor version update with lots of fixes, improvements and some new features.  Below are the major areas of improvement and the full release notes. To update your ColdBox installation just leverage CommandBox: `update coldbox`

## Event Caching Improvements

The event caching cleanup and clearing methods did not work when using granular query strings. This has now been resolved and optimized.

## New Auto-Deserialization of JSON Payloads

If you are working with any modern JavaScript framework, this feature is for you. ColdBox on any incoming request will inspect the HTTP Body content and if the payload is JSON, it will deserialize it for you and if it is a structure/JS object, it will append itself to the request collection for you. So if we have the following incoming payload:

```javascript
{
    "name" : "Jon Clausen",
    "type" : "awesomeness",
    "data" : [ 1,2,3 ]
}
```

The request collection will have 3 keys for **name**, **type** and **data** according to their native CFML type.

## Flash Scope getAll\(\)

The flash scope needed a way to get all of its name-value pair elements in one shot, you can now with the `getAll()` method.

## Complete Rewrite of the HTML Helper

The HTML helper has been completely rewritten in 5.1 into script notation, optimized for performance and security.  **All HTML output is now XSS encoded for attributes and tag content.**

## View and Directory Helper Combo

You can now declare a view and directory helper and ColdBox will use them both instead of always picking the view helper only.  The order of inclusion is:

* directory helper
* view helper
* view

## ColdBox Fail Fast

This is a nice feature that will give your applications stability when doing deployments or production reinits.  We have added a new application variable flag: **application.fwReinit** which is set to true when the framework is reinitializing and false when it completes.  We have also added a new directive called **COLDBOX\_FAIL\_FAST** which defaults to true.

If fail fast is activated, the framework will present a nice message to users that the application is not yet available instead of holding them in a queue waiting for the reinit or application load to finish.  This fail fast will release your traffic queue and produce less timeouts and failures.

The fail fast directive can also be a closure.  Then we will execute your closure and you can do whatever you like within it to advice your users' about the reinit.  Below you can see what happens with the fail fast code.

```javascript
// Global flag to denote if we are in mid reinit or not.
cfparam( name="application.fwReinit", default =false );

// Fail fast so users coming in during a reinit just get a please try again message.
if( application.fwReinit ){

    // Closure or UDF
    if( isClosure( variables.COLDBOX_FAIL_FAST ) || isCustomFunction( variables.COLDBOX_FAIL_FAST ) ){
        variables.COLDBOX_FAIL_FAST();
    } 
    // Core Fail Fast Option
    else if( isBoolean( variables.COLDBOX_FAIL_FAST ) && variables.COLDBOX_FAIL_FAST ){
        writeOutput( 'Oops! Seems ColdBox is still not ready to serve requests, please try again.' );
        // You don't have to return a 500, I just did this so JMeter would report it differently than a 200 
        cfheader( statusCode="503", statustext="ColdBox Not Available Yet!" );
    }

    return false;
}
```

## Release Notes

### Bugs

* \[[COLDBOX-679](https://ortussolutions.atlassian.net/browse/COLDBOX-679)\] - viewmodule parameter not used in system.web.renderer.renderLayout
* \[[COLDBOX-680](https://ortussolutions.atlassian.net/browse/COLDBOX-680)\] - When using Resources the POST incorrectly sets action to UPDATE instead of CREATE
* \[[COLDBOX-681](https://ortussolutions.atlassian.net/browse/COLDBOX-681)\] - AbstractFlashScope fails on autoPurge property check
* \[[COLDBOX-683](https://ortussolutions.atlassian.net/browse/COLDBOX-683)\] - Event Caching Should Include Response Headers
* \[[COLDBOX-686](https://ortussolutions.atlassian.net/browse/COLDBOX-686)\] - coldbox create app template doesn't work with a servlet context other than /
* \[[COLDBOX-687](https://ortussolutions.atlassian.net/browse/COLDBOX-687)\] - Event caching broken due to not evaluating renderdata as a valid struct thanks to the EC OIL Team \(Christian,Sebastian,Didier\)

### New Features

* \[[COLDBOX-682](https://ortussolutions.atlassian.net/browse/COLDBOX-682)\] - Add auto-deserialization of inbound JSON payloads into the RC on request capture
* \[[COLDBOX-689](https://ortussolutions.atlassian.net/browse/COLDBOX-689)\] - New flash method: getAll\(\) which retrieves a struct of all flash keys and their content
* \[[COLDBOX-693](https://ortussolutions.atlassian.net/browse/COLDBOX-693)\] - Complete rewrite of HTML Helper to Script
* \[[COLDBOX-694](https://ortussolutions.atlassian.net/browse/COLDBOX-694)\] - HTML Helper XSS Encodes all output from content to attributes by default

### Improvements

* \[[COLDBOX-343](https://ortussolutions.atlassian.net/browse/COLDBOX-343)\] - Allow view helper AND directory helper at the same time.
* \[[COLDBOX-592](https://ortussolutions.atlassian.net/browse/COLDBOX-592)\] - Have ColdBox bootstrap advertize when Coldbox is reinitting, and have a fail fast routine
* \[[COLDBOX-678](https://ortussolutions.atlassian.net/browse/COLDBOX-678)\] - Default Flash Ram to client if session scope is disabled
* \[[COLDBOX-685](https://ortussolutions.atlassian.net/browse/COLDBOX-685)\] - Event Cache Key and Storage Enhancements to allow for granular querystring evictions
* \[[COLDBOX-690](https://ortussolutions.atlassian.net/browse/COLDBOX-690)\] - Add support for cgi.https to isSSL\(\)
* \[[COLDBOX-691](https://ortussolutions.atlassian.net/browse/COLDBOX-691)\] - Ignore AllowedMethods when using runEvent on non-default method calls

