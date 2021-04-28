# HTML Helper

## Introduction

The HTML Helper is a core ColdBox CFC that abstracts the creation of any HTML entity. It provides consistent and secure rendering of HTML within ColdBox applications.

```text
coldbox.system.modules.HTMLHelper.modles.HTMLHelper.cfc
```

{% hint style="info" %}
Please check out the [latest CFC Docs ](http://apidocs.ortussolutions.com/coldbox/current)for all the methods available to the HTML Helper.
{% endhint %}

There is no special setup needed to use it in a ColdBox application, it's already baked in. Just reference the object by the `html` prefix and call the desired function within any layout or view.

```markup
// CFML
#html.button(name = "searchView", value = "View")#

// HTML Output
<button name="searchView" id="searchView" type="button">View<button>
```

By specifying the `name` of the element and not the `id`, both attributes are output using the same value. You can specify a different value for the `id` attribute by explicitly defining it.

```markup
// CFML w/ differnet name and id
#html.button(name = "searchView", id="id_searchView", value = "View")#

// HTML Output
<button name="searchView" id="id_searchView" type="button">View<button>
```

You can also pass in any attribute to the registered functions and if it does not match an argument it is passed through into the HTML element:

```markup
// CFML w/ differnet name and id
#html.button(
  name = "searchView", 
  id="id_searchView",
  value = "View",
  class = "btn btn-default"
)#

// HTML Output
<button 
  name="searchView" 
  id="id_searchView" 
  type="button" 
  class="btn btn-default">
  View
<button>
```

## Injecting the HTML Helper

You can inject the HTML helper anywhere you like by using the following registered mapping: `HTMLHelper@coldbox`

```javascript
property name="html" inject="HTMLHelper@coldbox";
```

