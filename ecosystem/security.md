---
description: Secure ColdBox applications with cbsecurity, covering rule-driven authorization, annotations, route middleware, JWT, sessions and CSRF.
icon: lock
---

# Security

ColdBox security is delivered by the **cbsecurity** module family: an engine that guards routes and handlers with authentication and authorization, over sessions or JWT. It is the recommended default, see the [Security](../digging-deeper/security/README.md) section for the full picture.

```bash
box install cbsecurity
```

📖 **Full documentation:** [coldbox-security.ortusbooks.com](https://coldbox-security.ortusbooks.com)

## Quick Example

Declare a rule in your `ColdBox` class and cbsecurity enforces it before your handlers run:

```javascript
// config/ColdBox.bx
moduleSettings = {
    cbauth : { userServiceClass : "UserService" },
    cbsecurity : {
        firewall : {
            invalidAuthenticationEvent : "main.login",
            invalidAuthorizationEvent  : "main.unauthorized",
            rules : [ { secureList : "^admin", permissions : "ADMIN" } ]
        }
    }
}
```

Or secure a single action with an annotation, or a route with middleware:

```javascript
// Annotation
function delete( event, rc, prc ) secured="ADMIN" {
    userService.delete( rc.id )
}

// config/Router.bx
route( "/admin" )
    .middleware( "Authorized@cbsecurity" )
    .meta( { permissions : "ADMIN" } )
    .to( "admin.index" )
```

## The Family

| Module | Role |
| --- | --- |
| `cbsecurity` | The firewall, annotations, route middleware, validators, JWT and security headers |
| `cbauth` | Authentication service: `authenticate()`, `isLoggedIn()`, `getUser()` |
| `cbcsrf` | CSRF tokens for forms and AJAX |
| `cbsecurity-passkeys` | WebAuthn/passkey login |
| `cbSSO` | Single sign-on across multiple ColdBox apps |

## When to Use It

Use cbsecurity for any application with users. Reach for a custom interceptor only for behavior cbsecurity does not cover, and keep it consistent with the firewall's [best practices](../digging-deeper/security/best-practices.md).

## See Also

- [Security](../digging-deeper/security/README.md) and [Security Best Practices](../digging-deeper/security/best-practices.md)
- [Route Middleware](../the-basics/routing/routing-dsl/middleware.md)
- [HTTP Method Security](../the-basics/event-handlers/http-method-security.md)
- [Validation](validation.md)
