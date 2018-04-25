# Adding Routes

### Routing By Convention

Every router has a default route already created for you, which we refer to as routing by convention:

```javascript
route( ":handler/:action?").end();
```

The URL pattern in the default route includes two special position **placeholders, **meaning that the handler and the action will come from the URL.

* `:handler` - The handler to relocate to \(It can include a Package and/or Module reference\)
* `:action` - The action to relocate to \(See the `?`, this means that the action is **optional**\)

{% hint style="success" %}
**Tip** The `:handler` parameter allows you to nest module names and handler names. Ex: `/module/handler/action`

If no action is passed the default action is `index`
{% endhint %}

This route can handle pretty much all your needs by convention. 

```javascript
// Basic Routing
http://localhost/general -> event=general.index
http://localhost/general/index -> event=general.index

// If 'admin' is a package/folder in the handlers directory
http://localhost/admin/general/index -> event=admin.general.index 

// If 'admin' is a module
http://localhost/admin/general/index -> event=admin:general.index
```

#### Convention Name-Value Pairs

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

### Route Placeholders

In your URL pattern you can also use the `:` syntax to denote a **variable position holder**. These position holders are **alpha-numeric** by default:

```javascript
route( "blog/:year/:month?/:day?", "blog.index" );
```

Once a URL is matched to the route, those placeholders will become request collection \(`RC`\) variables:

```text
http://localhost/blog/2012/12/22 -> rc.year=2012, rc.month=12, rc.day=22
```

#### Numeric Placeholders

ColdBox gives you also the ability to declare numerical only routes by appending `-numeric` to the variable placeholder so the route will only match if the placeholder is **numeric**. Let's modify the route from above.

```javascript
route( "blog/:year-numeric/:month-numeric?/:day-numeric?", "blog.index" );
```

This route will only accept years, months and days as numbers.

#### Alpha Placeholders

ColdBox gives you also the ability to declare alpha only routes by appending `-alpha` to the variable placeholder so the route will only match if the placeholder is `alpha` only.

```javascript
route( "wiki/:page-alpha", "wiki.show" );
```

This route will only accep page names that are alpha only.

