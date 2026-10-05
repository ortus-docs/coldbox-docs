---
description: A practical checklist for hardening ColdBox applications, from secure by default and protected secrets to defended forms and APIs.
icon: list-check
---

# Security Best Practices

Use this as a checklist. Each item links to the part of ColdBox or [cbsecurity](README.md) that implements it.

## Secure by Default

Deny everything, then allow what is public. Rules are evaluated in order and a rule's `whitelist` only skips that rule, so put your catch-all rule last. Patterns are comma-delimited regular expressions by default.

```javascript
rules : [
    // Public pages are whitelisted from this rule, everything else needs a login
    { secureList : ".*", whitelist : "^main\.(login|doLogin)$,^public\." }
]
```

Prefer permissions over roles when you check access. Roles change with your organization, permissions describe what the code does.

## Authenticate With cbauth, Hash With bcrypt

* Let `cbauth` own login state, using `authenticate()`, `isLoggedIn()` and `getUser()`. Do not keep your own flags in the session.
* Store only password hashes, created with the `bcrypt` module (`box install bcrypt`). Never use plain hashing functions for passwords.

## Keep Secrets Out of Source

Read secrets from the environment, never from committed files.

```javascript
jwt : { secretKey : getSystemSetting( "JWT_SECRET", "" ) }
```

## Protect Forms With CSRF Tokens

Every request that changes state needs a token, which `cbcsrf` provides through the `csrf()` and `csrfVerify()` helpers.

```html
<!-- In the form -->
#csrf()#
```

```javascript
// In the handler
function save( event, rc, prc ) {
    if ( !csrfVerify( rc.csrf ) ) {
        relocate( "main.login" )
    }
}
```

## Use JWT for APIs

Stateless APIs should not depend on server sessions. Secure them with the JWT validator and set token expiration and refresh tokens in the `jwt` settings.

```javascript
group( { pattern : "/api", middleware : [ "JwtAuth@cbsecurity" ] }, () => {
    route( "/orders" ).to( "orders.index" )
} )
```

Return a `block` action for APIs instead of redirecting to a login page: set `defaultAuthenticationAction : "block"` for API modules.

## Turn On the Security Headers

cbsecurity sends these by default: `X-Content-Type-Options`, `X-Frame-Options`, `Strict-Transport-Security`, `Referrer-Policy` and `X-XSS-Protection`. These are off by default, so enable them when they fit your app:

| Setting | When to enable |
| --- | --- |
| `securityHeaders.secureSSLRedirects.enabled` | Always in production, to redirect non-SSL requests. |
| `securityHeaders.contentSecurityPolicy` | Once you have written a policy for your assets. |
| `securityHeaders.hostHeaderValidation` | To reject unexpected `Host` headers, with an `allowedHosts` list. |
| `securityHeaders.trustUpstream` | Only when your proxies are trusted, so `x-forwarded-*` headers can be inspected first. |

## Treat All Input as Hostile

* Validate everything in `rc` with [cbvalidation](../../ecosystem/validation.md), and keep server-side state in `prc`.
* Restrict what each action accepts with [HTTP method security](../../the-basics/event-handlers/http-method-security.md).

## Harden Production

* Set a `reinitPassword` and never expose `?fwreinit=1` publicly.
* Leave the cbsecurity visualizer disabled (its default), or turn on `visualizer.secured` and give it a `securityRule`.

## Observe Denials

cbsecurity announces `cbSecurity_onInvalidAuthentication` and `cbSecurity_onInvalidAuthorization` for every denied request, whether it came from a rule, an annotation or route middleware. Listen to them to log, alert or rate limit.

## Test Your Security

Integration tests can assert denials end to end. With ColdBox 8.3+, `execute()`, `get()` and the other helpers also run route middleware.

```javascript
it( "redirects guests away from the admin area", () => {
    var event = get( "/admin" )
    expect( event.getValue( "relocate_event" ) ).toBe( "main.login" )
} )
```

## See Also

* [Security](README.md)
* [Route Middleware](../../the-basics/routing/routing-dsl/middleware.md)
* [cbsecurity documentation](https://coldbox-security.ortusbooks.com)
