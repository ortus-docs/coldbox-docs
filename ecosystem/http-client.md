---
description: Make outbound HTTP requests from ColdBox with hyper — a fluent, builder-style HTTP client.
icon: globe
---

# HTTP Client

**hyper** is the ecosystem's fluent HTTP client for calling external APIs — a builder-style alternative to raw `cfhttp`/`http()` calls.

```bash
box install hyper
```

📖 **Full documentation:** [hyper.ortusbooks.com](https://hyper.ortusbooks.com)

## Quick Example

```javascript
property name="hyper" inject="HyperBuilder@hyper";

var response = hyper
    .baseUrl( "https://api.stripe.com/v1" )
    .withHeaders( { "Authorization" : "Bearer #settings.stripeKey#" } )
    .post( "/charges", {
        "amount"   : 2000,
        "currency" : "usd",
        "source"   : rc.token
    } )

if ( response.isSuccess() ) {
    var charge = response.json()
}
```

## Why Not Raw `http()`?

- **Fluent, readable call sites** — headers, auth, query params, bodies compose naturally
- **Consistent response object** — `isSuccess()`, `json()`, `statusCode()` regardless of engine quirks
- **Testable** — fake and assert on requests in your test suite
- **Reusable clients** — pre-configure a builder per API and inject it as a singleton

## When to Use It

- Consuming third-party REST APIs
- Microservice-to-microservice calls
- Webhook delivery and verification

## See Also

- [Security](security.md) — signing and verifying webhook signatures
- [Queues](queues.md) — queue outbound calls that can tolerate latency
