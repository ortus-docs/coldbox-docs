# What's New With 7.0.0



## Release Notes

The full release notes per library can be found below. Just click on the library tab and explore their release notes:

{% tabs %}
{% tab title="ColdBox HMVC" %}
### Improvement

* [COLDBOX-1155](https://ortussolutions.atlassian.net/browse/COLDBOX-1155) Implement abort logic onAuthenticationFailure on RESTHandler
* [COLDBOX-1157](https://ortussolutions.atlassian.net/browse/COLDBOX-1157) Reuse existing controller in getMockRequestContext()
* [COLDBOX-1159](https://ortussolutions.atlassian.net/browse/COLDBOX-1159) JSON Serialization in \`forAttribute\` Does Not Support ACF Prefixing

### Bug

* [COLDBOX-1079](https://ortussolutions.atlassian.net/browse/COLDBOX-1079) Router resources doesn't seem to respect group() closures
* [COLDBOX-1100](https://ortussolutions.atlassian.net/browse/COLDBOX-1100) Event Caching Does Not Preserve HTTP Response Codes
* [COLDBOX-1133](https://ortussolutions.atlassian.net/browse/COLDBOX-1133) \`getFullURL\` encodes the query string when it should not.
* [COLDBOX-1136](https://ortussolutions.atlassian.net/browse/COLDBOX-1136) Scoping lookup bug in Lucee affects route()
* [COLDBOX-1138](https://ortussolutions.atlassian.net/browse/COLDBOX-1138) Event Cache Response Has Status Code of 0 (or Null)
* [COLDBOX-1139](https://ortussolutions.atlassian.net/browse/COLDBOX-1139) make event caching cache keys lower cased to avoid case issues when clearing keys
* [COLDBOX-1143](https://ortussolutions.atlassian.net/browse/COLDBOX-1143) render inline PDF (CB 6.8.1) throws a 500 error
* [COLDBOX-1145](https://ortussolutions.atlassian.net/browse/COLDBOX-1145) RestHandler OnError() Exception not checking for empty \`exception\` blocks which would cause another exception on development ONLY
* [COLDBOX-1146](https://ortussolutions.atlassian.net/browse/COLDBOX-1146) BiConsumer proxy was making both arguments required, when they can be null so execution fails
* [COLDBOX-1149](https://ortussolutions.atlassian.net/browse/COLDBOX-1149) Woops and Adobe CF needs a double check if session/client is defined even if sessionManagement/clientManagement is defined
* [COLDBOX-1150](https://ortussolutions.atlassian.net/browse/COLDBOX-1150) virtual app controller scoping is missing on ocassion due to this.load|unload flags
* [COLDBOX-1151](https://ortussolutions.atlassian.net/browse/COLDBOX-1151) Integration Tests do not support NoRender()
* [COLDBOX-1153](https://ortussolutions.atlassian.net/browse/COLDBOX-1153) RestHandler.cfc missing exception information on InvalidCredentials & TokenInvalidException
* [COLDBOX-1154](https://ortussolutions.atlassian.net/browse/COLDBOX-1154) Invalid DateFormat Mask in Whoops.cfm



### New Feature

* [COLDBOX-1039](https://ortussolutions.atlassian.net/browse/COLDBOX-1039) Allow unregistering closure listeners
* [COLDBOX-1140](https://ortussolutions.atlassian.net/browse/COLDBOX-1140) Whoops updates galore! SQL Syntax highlighting, json formatting and highlighting, and more
* [COLDBOX-1141](https://ortussolutions.atlassian.net/browse/COLDBOX-1141) New Flow delegate helpers for functional usage everywhere in ColdBox land
* [COLDBOX-1142](https://ortussolutions.atlassian.net/browse/COLDBOX-1142) New convention for module setting overrides: config/{moduleName}.cfc
* [COLDBOX-1147](https://ortussolutions.atlassian.net/browse/COLDBOX-1147) PostLayoutRender and PostViewRender now pass which view/layout path was used to render
* [COLDBOX-1148](https://ortussolutions.atlassian.net/browse/COLDBOX-1148) postEvent now get's new interceptData: ehBean, handler and data results
* [COLDBOX-1152](https://ortussolutions.atlassian.net/browse/COLDBOX-1152) this.unloadColdBox is false now as the default thanks to the virtual test app
* [COLDBOX-1158](https://ortussolutions.atlassian.net/browse/COLDBOX-1158) New \`back()\` function in super type that you can use to redirect back to your referer or a fallback
* [COLDBOX-1161](https://ortussolutions.atlassian.net/browse/COLDBOX-1161) new toJson() helper in the Util class which is delegated in many locations around the framework to add struct based query serialization and no dubm security prefixes
* [COLDBOX-1162](https://ortussolutions.atlassian.net/browse/COLDBOX-1162) Add in functionality to exclude patterns via router's findRoute()
* [COLDBOX-1163](https://ortussolutions.atlassian.net/browse/COLDBOX-1163) Route Interceptors
* [COLDBOX-1164](https://ortussolutions.atlassian.net/browse/COLDBOX-1164) New convention for coldfusion tags: includes/tags. Every ColdBox app will register that location as a repository for custom tags for your application
* [COLDBOX-1165](https://ortussolutions.atlassian.net/browse/COLDBOX-1165) New convention for modules for storing and using coldfusion tags: \`/tags\`
* [COLDBOX-1166](https://ortussolutions.atlassian.net/browse/COLDBOX-1166) Lazy loading and persistence of engine helper to assist in continued performance and initial load speed
* [COLDBOX-1167](https://ortussolutions.atlassian.net/browse/COLDBOX-1167) New core delegates for smaller building blocks, which leverages the \`@coreDelegates\` namespace
* [COLDBOX-1168](https://ortussolutions.atlassian.net/browse/COLDBOX-1168) New coldbox based delegates mapped with \`@cbDelegates\`
* [COLDBOX-1137](https://ortussolutions.atlassian.net/browse/COLDBOX-1137) Allow passing interception point first in interceptor listen() method



### Task

* [COLDBOX-1160](https://ortussolutions.atlassian.net/browse/COLDBOX-1160) COMPAT: jsonQueryFormat has been removed in preference to "struct".
{% endtab %}

{% tab title="WireBox" %}
### Improvement

* [WIREBOX-133](https://ortussolutions.atlassian.net/browse/WIREBOX-133) BeanPopulator was renamed to ObjectPopulator to be consistent with naming

### Bug

* [WIREBOX-132](https://ortussolutions.atlassian.net/browse/WIREBOX-132) WireBox caches Singletons even if their autowired dependencies throw exceptions.

### New Feature

* [WIREBOX-130](https://ortussolutions.atlassian.net/browse/WIREBOX-130) Ability to remove specific objects from wirebox injector singleton's and request scopes via a \`clear( key )\` method
* [WIREBOX-131](https://ortussolutions.atlassian.net/browse/WIREBOX-131) Object Delegators
* [WIREBOX-134](https://ortussolutions.atlassian.net/browse/WIREBOX-134) Object Populator is now created by the Injector and it is now a singleton
* [WIREBOX-135](https://ortussolutions.atlassian.net/browse/WIREBOX-135) Object populator now caches orm entity maps, so they are ONLy loaded once and population with orm objects accelerates tremendously
* [WIREBOX-136](https://ortussolutions.atlassian.net/browse/WIREBOX-136) object populator cache relational metadata for faster population of the same objects
* [WIREBOX-137](https://ortussolutions.atlassian.net/browse/WIREBOX-137) New \`this.population\` marker for controlling mas population of objects. It can include an \`include\` and and \`exclude\` list.
* [WIREBOX-138](https://ortussolutions.atlassian.net/browse/WIREBOX-138) Lazy Properties
* [WIREBOX-139](https://ortussolutions.atlassian.net/browse/WIREBOX-139) Property Observers
{% endtab %}

{% tab title="CacheBox" %}
### Bug

* [CACHEBOX-83](https://ortussolutions.atlassian.net/browse/CACHEBOX-83) Intermittent Exception from MetadataIndexer
{% endtab %}

{% tab title="LogBox" %}
####
{% endtab %}
{% endtabs %}
