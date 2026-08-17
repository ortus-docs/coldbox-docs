# Subdomain Routing

`withDomain()` scopes a route to a specific domain or subdomain - useful for multi-tenant or SaaS applications.

```javascript
route( "/" )
    .withDomain( "subdomain-routing.dev" )
    .to( "subdomain.index" );

route( "/" )
    .withDomain( ":username.forgebox.dev" )
    .to( "subdomain.show" );
```

The domain string can contain **placeholders**, just like a URL pattern - they're translated into `RC` variables the same way. Everything else in the routing DSL still applies; `withDomain()` is just one more modifier in the chain.

{% hint style="success" %}
**Tip:** [Routing Groups](routing-groups.md) can share a `withDomain()` across many routes at once - handy when a whole section of your app lives on its own subdomain.
{% endhint %}
