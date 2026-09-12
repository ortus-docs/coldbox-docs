# Proxy Events

The ColdBox Proxy also has a different life cycle than traditional MVC. All of a request's interception points fire with one addition: `preProxyResults`. This event fires right before the proxy returns results back to proxies. This is a great way to do transformations, logging, etc.

{% tabs %}
{% tab title="BoxLang" %}
```boxlang
class{

    function preProxyResults(event, interceptData){
        log.debug("Proxy request finalized: ", interceptData.proxyResults );
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component{

    function preProxyResults(event, interceptData){
        log.debug("Proxy request finalized: ", interceptData.proxyResults );
    }

}
```
{% endtab %}
{% endtabs %}
