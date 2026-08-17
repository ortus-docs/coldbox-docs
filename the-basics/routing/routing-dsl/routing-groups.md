# Routing Groups

There will be a time where your routes will become very verbose and you would like to group them into logical declarations.  These groupings can also help you **prefixes** repetitive patterns in many routes with a single declarative construct.  These needs are met with the `group()` method in the router.

```javascript
function group( struct options={}, body );
```

The best way to see how it works is by example:

```javascript
route( pattern="/news", target="public.news.index" );
route( pattern="/news/recent", target="public.news.recent" );
route( pattern="/news/removed", target="public.news.removed" );
route( pattern="/news/add/:title", target="public.news.add" );
route( pattern="/news/delete/:slug", target="public.news.remove" );
route( pattern="/news/v/:slug", target="public.news.view" );
```

As you can see from the routes above, we have lots of repetitive code that we can clean out. So let's look at the same routes but using some nice grouping action.

```javascript
group( { pattern="/news", target="public.news." }, function(){
	route( "/", "index" )
	.route( "/recent", "recent" )
	.route( "/removed", "removed" )
	.route( "/add/:title", "add" )
	.route( "/delete/:slug", "remove" )
	.route( "/v/:slug", "view" );
} );
```

The **options** struct can contain any values that you can use within the closure.  Grouping can also be very nice when creating [namespaces](routing-namespaces.md).

## Sharing More Than Patterns

A group's `options` struct isn't limited to `pattern`/`target`. Any modifier a route accepts can be shared the same way - `handler`, `module`, `namespace`, `domain`, and `middleware` all apply to every route declared inside the body:

```javascript
group( { pattern : "/api", middleware : [ "RequireApiKey" ] }, function(){
    route( "/users" ).toHandler( "users" );
    route( "/products" ).toHandler( "products" );
} );
```

{% hint style="success" %}
**Next:** see [Route Middleware](middleware.md) for how a shared `middleware` list actually runs, and [Middleware Groups & Exclusions](middleware-groups.md) for reusing one by name across unrelated groups.
{% endhint %}

