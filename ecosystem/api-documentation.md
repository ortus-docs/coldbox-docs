---
description: Generate OpenAPI documentation for your ColdBox APIs automatically with cbswagger.
icon: file-code
---

# API Documentation

**cbswagger** generates OpenAPI (v3) documentation directly from your ColdBox routes and handler metadata — your API docs stay in sync with your code because they come from it.

```bash
box install cbswagger
```

📖 **Full documentation:** [ForgeBox](https://forgebox.io/view/cbSwagger) · [GitHub](https://github.com/coldbox-modules/cbSwagger)

## Quick Example

Install it, and your route table becomes an OpenAPI document:

```javascript
// config/Router.bx — routes you already have
route( "/api/users" ).toHandler( "users.index" )
route( "/api/users/:id" ).toHandler( "users.show" )
```

Then enrich handlers with metadata cbswagger reads:

{% tabs %}
{% tab title="BoxLang" %}
```javascript
/**
 * @route GET /api/users
 * @summary List all users
 * @tags Users
 * @response 200 {array} List of users
 */
function index( event, rc, prc ){
    event.renderData( type : "json", data : userService.list() )
}
```
{% endtab %}
{% tab title="CFML" %}
```javascript
/**
 * @route GET /api/users
 * @summary List all users
 * @tags Users
 * @response 200 {array} List of users
 */
function index( event, rc, prc ){
    event.renderData( type : "json", data : userService.list() )
}
```
{% endtab %}
{% endtabs %}

The generated spec is served from your app (by default at `/cbswagger`) and feeds Swagger UI, Postman, or any OpenAPI tooling.

## When to Use It

- Public APIs that need always-current documentation
- Teams generating client SDKs from an OpenAPI spec
- Contract testing against a live spec

## See Also

- [Resourceful Routes](../the-basics/routing/routing-dsl/resourceful-routes.md) — the route shapes cbswagger documents best
- [REST Handler](../digging-deeper/rest-handler.md) — building the APIs it documents
