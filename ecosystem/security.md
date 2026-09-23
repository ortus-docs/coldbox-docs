---
description: Secure ColdBox applications with cbsecurity — rule-driven authorization, JWT, sessions, CSRF, and passkeys.
icon: lock
---

# Security

ColdBox security is delivered by the **cbsecurity** module family: a rule-driven engine that guards routes and handlers with authentication and authorization, over sessions or JWT.

```bash
box install cbsecurity
```

📖 **Full documentation:** [coldbox-security.ortusbooks.com](https://coldbox-security.ortusbooks.com)

## Quick Example

Declare rules in your `ColdBox` class and cbsecurity enforces them before your handlers run:

```javascript
// config/ColdBox.bx
cbsecurity : {
    rules : [
        { secureList : "^/admin", permissions : "admin" },
        { secureList : "^/api",   permissions : "api:read" }
    ],
    rulesSource : "model",
    validator   : "CBAuthValidator@cbauth"
}
```

```javascript
// Secure a single action with an annotation
function delete( event, rc, prc ) secured="admin" {
    userService.delete( rc.id )
}
```

## The Family

| Module | Role |
| --- | --- |
| `cbsecurity` | The rules engine, validators, and firewall |
| `cbauth` | Authentication service — `authenticate()`, `isLoggedIn()`, `getUser()` |
| `cbcsrf` | CSRF tokens for forms and AJAX |
| `cbsecurity-passkeys` | WebAuthn/passkey login |
| `cbSSO` | Single sign-on across multiple ColdBox apps |

```javascript
// CSRF protection in a form
<input type="hidden" name="_token" value="#csrfToken()#">
```

```javascript
// Verify in the handler
if ( !csrfVerify( rc._token ) ) {
    relocate( "login" )
}
```

## Best Practices

- **Validate everything from `rc`** — treat all URL/FORM input as hostile; pair with [cbvalidation](validation.md)
- **PRC for internals, RC for input** — never trust `rc` to carry server-side state
- **Set a `reinitPassword`** in production and never expose `?fwreinit=1` publicly
- **Prefer JWT for APIs** — cbsecurity's JWT validator removes server session affinity
- **Hash passwords with `bcrypt`** (`box install bcrypt`), never with plain hashing functions

## See Also

- [HTTP Method Security](../the-basics/event-handlers/http-method-security.md)
- [Route Middleware](../the-basics/routing/routing-dsl/middleware.md) — route-scoped guards
- [HTTP Method Spoofing](../the-basics/routing/http-method-spoofing.md) — hardening notes
