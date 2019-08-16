# What's New With 5.6.0

ColdBox 5.6.0 is a minor version update with lots of fixes, improvements, performance enhancements and some nice new features.  Below are the major areas of improvement and the full release notes. To update  ColdBox or any of the standalone libraries  just leverage CommandBox: 

* `update coldbox`
* `update logbox`
* `update wirebox`
* `update cachebox`

## Major Updates

### Performance

We had two specific tickets that have resulted in extreme performance improvements for ALL ColdBox requests.  You will feel and see the difference:

* \[[COLDBOX-799](https://ortussolutions.atlassian.net/browse/COLDBOX-799)\] - Event Handler Bean: Single instance per handler action for major performance improvements

This ticket was contributed by Dom Watson \([https://twitter.com/dom\_watson](https://twitter.com/dom_watson)\) one of the lead engineers of the amazing PresideCMS project built on top of ColdBox.  We worked together to avoid the creation of handler beans on each runnable event.  We now cache each event handler bean representation which results in an extreme boost in performance.  Thanks Dom!

* \[[COLDBOX-810](https://ortussolutions.atlassian.net/browse/COLDBOX-810)\] - Remove afterInstanceAutowire interceptor in handlerService as afterHandlerCreation is now officially removed.

Thanks to our local mad scientist Brad Wood, he reported that the handler services still listened to ALL CFC creations in an application in order to relay an `afterHandlerCreation` interception point from the good 'ol 2.6 days.  This has been finally removed and boom, another big boost in performance!

### Better Bug Reports

We have enhanced the bug reporting templates to include much more information when dealing with exceptions:

* Show SQL error details on Adobe CF
* Current route, params and debug info
* Contributing module for the current routed URL

### Merging of HTTP Verbs

Thanks to our very own Eric Peterson, you can now merge HTTP verbs on the same route pattern, which you could not do before:

```java
router
    .post( "photos/", "photos.create" )
    .get( "photos/", "photos.index" )
    .delete( "photos/", "photos.remove" );
```

## ColdBox Core  Release Notes

### Bugs

* \[[COLDBOX-778](https://ortussolutions.atlassian.net/browse/COLDBOX-778)\] - ModuleService to add default route doesn't work correctly
* \[[COLDBOX-794](https://ortussolutions.atlassian.net/browse/COLDBOX-794)\] - Fix default bug report to show SQL error detail for adobe SQL exceptions
* \[[COLDBOX-796](https://ortussolutions.atlassian.net/browse/COLDBOX-796)\] - When doing package resolving if you are in a module it still tries to resolve a module
* \[[COLDBOX-806](https://ortussolutions.atlassian.net/browse/COLDBOX-806)\] - Error in HTML helper WRAPPERATTRS doesn't exist in argument scope
* \[[COLDBOX-811](https://ortussolutions.atlassian.net/browse/COLDBOX-811)\] - Include the colon for non 80 or 443 port numbers \#419 in github

### New Features

* \[[COLDBOX-812](https://ortussolutions.atlassian.net/browse/COLDBOX-812)\] - Allow merging of HTTP verbs when doing separate verbs for the same route
* \[[COLDBOX-813](https://ortussolutions.atlassian.net/browse/COLDBOX-813)\] - Update cfconfig to use env variables instead of inline mixins, modernizeOrDie

### Improvements

* \[[COLDBOX-795](https://ortussolutions.atlassian.net/browse/COLDBOX-795)\] - Add more current route information to the BugReport.cfm template
* \[[COLDBOX-797](https://ortussolutions.atlassian.net/browse/COLDBOX-797)\] - Ability for bug reports and app to know which module contributed the incoming URL route.
* \[[COLDBOX-798](https://ortussolutions.atlassian.net/browse/COLDBOX-798)\] - Use of .keyExists\(\) can needlessly use memory in requests, suggest StructKeyExists\(\) instead
* \[[COLDBOX-799](https://ortussolutions.atlassian.net/browse/COLDBOX-799)\] - Event Handler Bean: Single instance per handler action for major performance improvements
* \[[COLDBOX-800](https://ortussolutions.atlassian.net/browse/COLDBOX-800)\] - HandlerService.cfc$newHandler\(\): declares variables that are never used
* \[[COLDBOX-810](https://ortussolutions.atlassian.net/browse/COLDBOX-810)\] - Remove afterInstanceAutowire interceptor in handlerService as afterHandlerCreation is now officially removed.

## CacheBox Release Notes

### Bugs

* \[[CACHEBOX-56](https://ortussolutions.atlassian.net/browse/CACHEBOX-56)\] - AbstractCacheProvider.getOrSet\(\): local var unscoped when checking if null

