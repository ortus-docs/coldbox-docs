# What's New With 6.3.0

ColdBox 6.3.0 is a minor release that squashes lots of bugs and does tons of improvements for performance!

{% tabs %}
{% tab title="ColdBox HMVC" %}
## Bug

* \[[COLDBOX-890](https://ortussolutions.atlassian.net/browse/COLDBOX-890)\] - Renderer methods assume the module exists and throws exception when sending invalid url data
* \[[COLDBOX-914](https://ortussolutions.atlassian.net/browse/COLDBOX-914)\] - Can no longer have duplicate routes with different conditions
* \[[COLDBOX-935](https://ortussolutions.atlassian.net/browse/COLDBOX-935)\] - Colon \(:\) in URL Path Causes Exception Error
* \[[COLDBOX-964](https://ortussolutions.atlassian.net/browse/COLDBOX-964)\] - invalidEventHandler does not work when calling invalid action on valid handler
* \[[COLDBOX-967](https://ortussolutions.atlassian.net/browse/COLDBOX-967)\] - autowire annotation for test cases is not working as it should
* \[[COLDBOX-968](https://ortussolutions.atlassian.net/browse/COLDBOX-968)\] - Fix declaring multiple resources at once
* \[[COLDBOX-978](https://ortussolutions.atlassian.net/browse/COLDBOX-978)\] - AsyncManager threads don't release DB connections to pool for Adobe CF

## New Feature

* \[[COLDBOX-973](https://ortussolutions.atlassian.net/browse/COLDBOX-973)\] - Add new exception type catch for the RestHandler: \`**PermissionDenied**\` to trap in valid authorizations

## Improvement

* \[[COLDBOX-965](https://ortussolutions.atlassian.net/browse/COLDBOX-965)\] - Content type http header bypasses requestContext with render data - set explicit http header via request context
* \[[COLDBOX-971](https://ortussolutions.atlassian.net/browse/COLDBOX-971)\] - Implement caching strategy for application helper lookups into the \`template\` cache
* \[[COLDBOX-972](https://ortussolutions.atlassian.net/browse/COLDBOX-972)\] - Coldbox DataMarshaller Throws Error with Lucee-Light Engine
* \[[COLDBOX-974](https://ortussolutions.atlassian.net/browse/COLDBOX-974)\] - Have the html helper manifests in local memory instead of the template cache to avoid cleanup issues
* \[[COLDBOX-975](https://ortussolutions.atlassian.net/browse/COLDBOX-975)\] - Remove unecessary locks for view path setups in the renderer
* \[[COLDBOX-976](https://ortussolutions.atlassian.net/browse/COLDBOX-976)\] - Remove unecessary lock in the bootstrap to get the controller reference, it's already there for the reload checks
* \[[COLDBOX-979](https://ortussolutions.atlassian.net/browse/COLDBOX-979)\] - Module service now profiles registration and activation into the logs with the version and path of a module
{% endtab %}

{% tab title="CacheBox" %}
## Bug

* \[[CACHEBOX-67](https://ortussolutions.atlassian.net/browse/CACHEBOX-67)\] - `getStoreMetadataReport()` - wrong order of the `reduce()` parameters
{% endtab %}

{% tab title="WireBox" %}
## Improvement

* \[[WIREBOX-111](https://ortussolutions.atlassian.net/browse/WIREBOX-111)\] - Refactor the way `cffeed` is used so that ACF 2021 doesn't choke on first startups, only when used
{% endtab %}
{% endtabs %}

