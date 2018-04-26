# Routing By Convention

Every router has a **default route** already defined for you in the application templates, which we refer to as routing by convention:

```javascript
route( ":handler/:action?").end();
```

The URL pattern in the default route includes two special position **placeholders, **meaning that the handler and the action will come from the URL. Also note that the `:action` has a question mark \(`?`\), which makes the placeholder optional, meaning it can exist or not from the incoming URL.

* `:handler` - The handler to execute \(It can include a Package and/or Module reference\)
* `:action` - The action to relocate to \(See the `?`, this means that the action is **optional**\)

{% hint style="info" %}
Behind the scenes the router creates two routes due to the optional placeholder

1. route\( "/:handler/:action" \)
2. route\( "/:handler\)
{% endhint %}

{% hint style="success" %}
**Tip** The `:handler` parameter allows you to nest module names and handler names. Ex: `/module/handler/action`

If no action is passed the default action is `index`
{% endhint %}

This route can handle pretty much all your needs by convention:

```javascript
// Basic Routing
http://localhost/general -> event=general.index
http://localhost/general/index -> event=general.index

// If 'admin' is a package/folder in the handlers directory
http://localhost/admin/general/index -> event=admin.general.index 

// If 'admin' is a module
http://localhost/admin/general/index -> event=admin:general.index
```

### Convention Name-Value Pairs

Any extra name-value pairs in the remaining URL of a discovered URL pattern will be translated to variables in the request collection \(`rc`\) for you automagically. 

```text
http://localhost/general/show/page/2/name/luis
# translate to
event=general.show, rc.page=2, rc.name=luis

http://localhost/users/show/export/pdf/name
# translate to
event=users.show, rc.export=pdf, rc.name={empty value}
```

{% hint style="success" %}
**Tip:** You can turn this feature off by using the `valuePairTranslation( false )` modifier in the routing DSL on a route by route basis

`route( "/pattern" ).to( "users.show" ).valuePairTranslation( false );`
{% endhint %}



