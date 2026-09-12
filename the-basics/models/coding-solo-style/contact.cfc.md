# Contact.cfc

An object that represents a contact and self-validates using the `cbvalidation` module.

```
coldbox create model name=Contact --open
```

Then spice up with some properties and constraints:

{% tabs %}
{% tab title="BoxLang" %}
```boxlang
class accessors="true"{

    // properties
    property name="firstName";
    property name="lastName";
    property name="email";

    // validation
    this.constraints = {
        firstName = {required=true},
        lastName = {required=true},
        email = {required=true, type="email"}
    };

    function init(){
        return this;
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component accessors="true"{

    // properties
    property name="firstName";
    property name="lastName";
    property name="email";

    // validation
    this.constraints = {
        firstName = {required=true},
        lastName = {required=true},
        email = {required=true, type="email"}
    };

    function init(){
        return this;
    }

}
```
{% endtab %}
{% endtabs %}
