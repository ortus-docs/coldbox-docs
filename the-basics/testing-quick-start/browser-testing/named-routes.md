---
description: >-
  Build, visit and assert ColdBox named routes in browser tests with
  routeURL(), visitRoute() and assertRouteIs(), including module routes.
icon: route
---

# Named Routes

Hard-coded URLs make browser tests brittle: change a route pattern and every spec that visits it breaks. `BaseTestCase` builds URLs from your **named routes** instead, using the same `event.route()` your views use, so specs follow your router.

```javascript
// config/Router.bx
route( "/users/:id" ).as( "users.show" ).to( "users.show" )
route( "/users" ).as( "users.index" ).to( "users.index" )
```

```javascript
it( "opens a user from the list", () => {
	browse( ( page ) => {
		visitRoute( page, "users.index" )
		page.click( "Luis Majano" )
		assertRouteIs( page, "users.show", { id : 1 } )
	} )
} )
```

## routeURL()

`routeURL( name, [params] )` returns the **path** of a named route, without scheme and host. It is built by ColdBox's own `event.route()`, so it includes the routing prefix of your application and, for module routes, the module entry point.

```javascript
routeURL( "users.show", { id : 5 } )   // /users/5/
routeURL( "users.index" )              // /users/
routeURL( "posts@blog" )               // /blog/posts/
routeURL( "blog:posts" )               // /blog/posts/
```

It throws an `InvalidArgumentException` when the named route does not exist, so a renamed route fails loudly. Because the result is a path, it resolves against the spec `baseURL` when you visit it, and you can use it anywhere you need a link, for example `page.visit( routeURL( "search" ) & "?q=coldbox" )`.

## visitRoute()

`visitRoute( page, name, [params] )` is a shortcut for `page.visit( routeURL( name, params ) )`. It returns the page, so you can keep chaining:

```javascript
visitRoute( page, "users.show", { id : 5 } )
	.click( "Edit" )
	.fill( "Name", "Luis" )
	.click( "Save" )
```

## assertRouteIs()

`assertRouteIs( page, name, [params] )` asserts that the page is on a named route. It waits for the URL with bx-playwright's `waitForUrl()`, so it works right after a click or a form post that redirects.

| Call | Passes when |
| --- | --- |
| `assertRouteIs( page, "users.show" )` | The page path matches the route **pattern**: any value of its placeholders passes (`/users/5`, `/users/abc`) |
| `assertRouteIs( page, "users.show", { id : 5 } )` | The page path is the path of `routeURL( "users.show", { id : 5 } )` |

A route with optional placeholders, such as `route( "/posts/:id?" ).as( "posts" )`, matches with and without them: `assertRouteIs( page, "posts" )` passes on `/posts` and on `/posts/12`.

{% hint style="warning" %}
Optional placeholders are only fully supported **without** params. `routeURL()`, `visitRoute()` and `assertRouteIs()` build the path with `event.route()`, which resolves a route like `/posts/:id?` to its base path and drops the optional param: `routeURL( "posts", { id : 12 } )` returns `/posts/`, not `/posts/12/`. To visit or assert a specific optional value, build the path yourself, for example `page.visit( routeURL( "posts" ) & "12" )`, or give that URL its own named route.
{% endhint %}

Like ColdBox routing, the match:

* ignores case and the trailing slash (`/users/5`, `/users/5/` and `/USERS/5` all match)
* ignores the query string and the hash (`/users/5?tab=posts` and `/users/5#bio` match)
* does **not** match longer paths (`/users/5/posts` is not `users.show`)
* honors the route constraints, because it uses the regex ColdBox builds from the pattern

When the page is not on the route before the bx-playwright assertion timeout (`timeouts.assertion`, 5 seconds by default), the spec fails with a `TestBox.AssertionFailed` that names the expected route and the actual path:

```
Expected the page to be on route [users.show] with params {"id":5}, but the path is [/users/6/]
```

## Module Routes

Module routes use the same naming as `event.route()`: `name@module` or `module:name`. The helpers add the module entry point for you, including the inherited entry point of nested modules.

```javascript
// modules_app/blog/config/Router.bx (module entry point: blog)
route( "/posts" ).as( "posts" ).to( "posts.index" )
route( "/posts/:slug" ).as( "post" ).to( "posts.show" )
```

```javascript
it( "opens a blog post", () => {
	browse( ( page ) => {
		visitRoute( page, "posts@blog" )
		page.click( "Hello World" )
		assertRouteIs( page, "post@blog" )
		assertRouteIs( page, "blog:post", { slug : "hello-world" } )
	} )
} )
```

{% hint style="info" %}
ColdBox 8.3.0 also fixes `event.route( "name@module" )`, which built module links without a slash between the module entry point and the route pattern. Module links now always join them with a single slash.
{% endhint %}

## Different URL Prefixes

`routeURL()` uses the SES base URL of the **virtual** application your spec loads. If the server under test reaches the application through a different prefix, for example `/index.cfm` when URL rewrites are off, set the base URL the paths should use in a `beforeEach()`:

```javascript
beforeEach( ( currentSpec ) => {
	getRequestContext().setSESBaseURL( "http://127.0.0.1:8080/index.cfm" )
} )
```

Only the path of that URL is used: the browser still resolves it against `baseURL`.

## See Also

* [Named Routes in the Routing DSL](../../routing/routing-dsl/named-routes.md)
* [Building Routable Links](../../routing/building-routable-links.md)
* [Writing Browser Tests](writing-browser-tests.md)
* [Authentication](authentication.md)
