---
description: February 17, 202
---

# What's New With 6.6.1

## Release Notes

{% tabs %}
{% tab title="ColdBox HMVC" %}
**Bugs**

* [COLDBOX-1093](https://ortussolutions.atlassian.net/browse/COLDBOX-1093) Remove debug writedumps left over from previous testing
* [COLDBOX-1085](https://ortussolutions.atlassian.net/browse/COLDBOX-1085) Fix instance of bad route merging the routes but loosing the handler

**Minor Improvements**

* [COLDBOX-1095](https://ortussolutions.atlassian.net/browse/COLDBOX-1095) Update Response Pagination Properties for Case-Sensitive Engines
* [COLDBOX-1091](https://ortussolutions.atlassian.net/browse/COLDBOX-1091) default status code to 302 in the internal relocate() just like CFML does instead of 0 and eliminate source
* [COLDBOX-1089](https://ortussolutions.atlassian.net/browse/COLDBOX-1089) Update the internal cfml engine checker to have more engine based feature checkers
* [COLDBOX-1088](https://ortussolutions.atlassian.net/browse/COLDBOX-1088) Switch `isInstance` check on renderdata in controller to secondary of $renderdata check to optimize speed
{% endtab %}

{% tab title="CacheBox" %}
**Bugs**

* [CACHEBOX-80](https://ortussolutions.atlassian.net/browse/CACHEBOX-80) Bug in JDBCMetadataIndexer sortedKeys() using non-existent variable `arguments.objectKey`

**Minor Improvements**

* [CACHEBOX-81](https://ortussolutions.atlassian.net/browse/CACHEBOX-81) JDBCStore Dynamically generate queryExecute options + new config to always include DSN due to ACF issues


{% endtab %}
{% endtabs %}
