# Listening

Once your custom interception or event points are registered and CFC are registered then you can write the methods for listening to those events just like any other interceptor event:

{% tabs %}
{% tab title="BoxLang" %}
```boxlang
class{

    function onLog(event,data,buffer){
        // your code here
    }

    function onRecordInserted(event,data,buffer){
        // your code here
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component{

    function onLog(event,data,buffer){
        // your code here
    }

    function onRecordInserted(event,data,buffer){
        // your code here
    }

}
```
{% endtab %}
{% endtabs %}
