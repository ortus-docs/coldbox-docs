---
description: Server-side validation for ColdBox applications with the cbvalidation module — constraints, results, and handler integration.
icon: shield-check
---

# Validation

ColdBox provides server-side validation through the **cbvalidation** module: a unified engine for validating objects, structs, and forms with declared constraints.

```bash
box install cbvalidation
```

📖 **Full documentation:** [cbvalidation.ortusbooks.com](https://cbvalidation.ortusbooks.com)

## Quick Example

Declare constraints on a class, then validate it anywhere:

{% tabs %}
{% tab title="BoxLang" %}
```javascript
// models/User.bx
class {

    constraints = {
        "email"    : { required : true, type : "email" },
        "password" : { required : true, size : "8..64" }
    }

    property name="email"
    property name="password"
}
```
{% endtab %}
{% tab title="CFML" %}
```javascript
// models/User.cfc
component {

    this.constraints = {
        "email"    : { required : true, type : "email" },
        "password" : { required : true, size : "8..64" }
    }

    property name="email"
    property name="password"
}
```
{% endtab %}
{% endtabs %}

```javascript
// In a handler or service
property name="validationManager" inject="ValidationManager@cbvalidation";

function save( event, rc, prc ){
    var user   = populateModel( "User" )
    var result = validationManager.validate( target : user )

    if ( result.hasErrors() ) {
        return event.renderData(
            type       : "json",
            data       : { "errors" : result.getAllErrors() },
            statusCode : 422
        )
    }

    userService.save( user )
    event.renderData( type : "json", data : user.getMemento(), statusCode : 201 )
}
```

The result object gives you `hasErrors()`, `getAllErrors()`, `getError( field )`, and locale-aware messages via `cbi18n`.

## When to Use It

- Validating incoming `rc` data before it reaches your models
- Validating ORM/Quick entities before persistence
- Reusing the same constraint set across handlers, services, and APIs

ColdBox's `populateModel()` and event handler [model data binding](../the-basics/event-handlers/model-integration/model-data-binding.md) pair naturally with cbvalidation — populate first, validate second.

## Alternatives

- **RuleBox** (`box install rulebox`) — when your validation is really a business-rules decision tree rather than field constraints
- Native engine validation (`isValid()`) — fine for one-off checks, but doesn't scale to constraint sets

## See Also

- [Event Handler Validation](../the-basics/event-handlers/validation.md)
- [Model Data Binding](../the-basics/event-handlers/model-integration/model-data-binding.md)
- [i18n](i18n.md) — localized validation messages
