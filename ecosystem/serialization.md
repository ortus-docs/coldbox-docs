---
description: Serialize objects to structs and JSON in ColdBox with mementifier — composable, fast API responses.
icon: arrow-right-arrow-left
---

# Serialization

**mementifier** turns your objects into plain structs (their "memento") with fine-grained control — the standard way to build JSON API responses in ColdBox.

```bash
box install mementifier
```

📖 **Full documentation:** [mementifier.ortusbooks.com](https://mementifier.ortusbooks.com)

## Quick Example

{% tabs %}
{% tab title="BoxLang" %}
```javascript
// models/User.bx
class accessors="true" {

    this.memento = {
        defaultIncludes : [ "id", "email", "name" ],
        defaultExcludes : [ "password" ]
    }

    property name="id"
    property name="email"
    property name="name"
    property name="password"
}
```
{% endtab %}
{% tab title="CFML" %}
```javascript
// models/User.cfc
component accessors="true" {

    this.memento = {
        defaultIncludes : [ "id", "email", "name" ],
        defaultExcludes : [ "password" ]
    }

    property name="id"
    property name="email"
    property name="name"
    property name="password"
}
```
{% endtab %}
{% endtabs %}

```javascript
// In a handler
function show( event, rc, prc ){
    var user = userService.get( rc.id )

    event.renderData(
        type : "json",
        data : user.getMemento()   // password never leaves the server
    )
}
```

## Why Not `serializeJSON( user )`?

Raw serialization leaks internal state, follows relationships infinitely deep, and breaks on lazily-loaded ORM proxies. mementifier gives you an explicit contract:

- `defaultIncludes` / `defaultExcludes` — the default shape
- Per-call overrides: `user.getMemento( includes : [ "roles" ] )`
- Nested mementos for relationships, with their own include rules
- Custom formatters per property (dates, masks, computed values)

## When to Use It

- JSON API responses (pairs with the [REST Handler](../digging-deeper/rest-handler.md))
- Serializing ORM/Quick entities safely
- Any time an object crosses a boundary: APIs, queues, caches

## See Also

- [Data & Persistence](data-and-persistence.md) — ORM options
- [Rendering Data](../the-basics/event-handlers/rendering-data.md) — `renderData()` types
