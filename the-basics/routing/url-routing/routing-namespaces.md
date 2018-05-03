# Routing Namespaces

You can create a-la-carte namespaces for URL routes. **Namespaces** are cool groupings of routes according to a specific URL entry point. So you can say that all URLs that start with `/testing` will be found in the **testing** namespace and it will iterate through the namespace routes until it matches one of them. 

Much how modules work, where you have a module entry point,  you can create virtual entry point to **ANY** route by namespacing it. This route can be a module a non-module, package, or whatever you like. You start off by registering the namespace using the `addNamespace( pattern, namespace )` method or the fluent `route().toNamespaceRouting()` method.

```javascript
addNamespace( pattern="/testing", namespace="test" );
route( "/testing" ).toNamespaceRouting( "test" );

addNamespace( pattern="/news", namespace="blog" );
route( "/news" ).toNamespaceRouting( "blog" );
```

Once you declare the namespace you can use the grouping functionality to declare all the namespace routes or you can use a `route().withNamespace()` combination.

```javascript
// Via Grouping
route( "/news" ).toNamespaceRouting( "blog" )
	.group( { namespace = "blog" }, function(){
		route( "/", "blog.index" )
  		.route( "/:year-numeric?/:month-numeric?/:day-numeric?", "blog.archives" );
	} );
  

// Via Routing DSL
addNamespace( "/news", "blog" );
  
route( "/" )
  .withNameSpace( "blog" )
  .to( "blog.index" );

route( "/:year-numeric?/:month-numeric?/:day-numeric?" )
  .withNameSpace( "blog" )
  .to( "blog.archives" );

```

{% hint style="info" %}
**Hint** You can also register multiple URL patterns that point to the same namespace
{% endhint %}

