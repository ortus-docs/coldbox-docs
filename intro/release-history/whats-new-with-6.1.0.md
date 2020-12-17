# What's New With 6.1.0

ColdBox 6.1.0 is a minor release sporting fixes and a few minor updates to make your coding life easier 😂.

{% tabs %}
{% tab title="ColdBox HMVC" %}
### Bugs

* \[[COLDBOX-920](https://ortussolutions.atlassian.net/browse/COLDBOX-920)\] - `RenderLayout` throws exception when called multiple times in single request with explicit view
* \[[COLDBOX-921](https://ortussolutions.atlassian.net/browse/COLDBOX-921)\] - Adobe compat for null checks on exception beans
* \[[COLDBOX-927](https://ortussolutions.atlassian.net/browse/COLDBOX-927)\] - Can't disable session management

### New Features

* \[[COLDBOX-928](https://ortussolutions.atlassian.net/browse/COLDBOX-928)\] - Buildlink's `queryString` can now be a `struct` and it will be converted to the string equivalent for you

### Improvements

* \[[COLDBOX-922](https://ortussolutions.atlassian.net/browse/COLDBOX-922)\] - Whoops can be slow while dumping out CFC instances
{% endtab %}

{% tab title="WireBox" %}
### Bugs

* \[[WIREBOX-82](https://ortussolutions.atlassian.net/browse/WIREBOX-82)\] - builder.toVirtualInheritance\(\): scoping issues
* \[[WIREBOX-83](https://ortussolutions.atlassian.net/browse/WIREBOX-83)\] - When using sandbox security, and using a provider DSL the file existence checks blow up
{% endtab %}

{% tab title="LogBox" %}
### Bugs

* \[[LOGBOX-53](https://ortussolutions.atlassian.net/browse/LOGBOX-53)\] - Direct console debugging is left in the `AbstractAppender` and `FileAppender`
{% endtab %}
{% endtabs %}

