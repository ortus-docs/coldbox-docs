---
description: Database access in ColdBox — ORM options (BoxLang ORM, cborm, Quick), the qb query builder, and cfmigrations.
icon: database
---

# Data & Persistence

ColdBox is persistence-agnostic: you choose the data layer per project. The ecosystem offers three ORM paths, a fluent query builder, and migrations.

## Choosing an ORM

| Option | Engine | Best for |
| --- | --- | --- |
| **BoxLang ORM** (`bx-orm`) | BoxLang | New BoxLang apps — native, first-class |
| **cborm** | Adobe / Lucee / BoxLang-CFML | Existing Hibernate ORM apps, virtual service layers, criteria queries |
| **Quick** | All | Lightweight entity ORM without Hibernate |

```bash
box install bx-orm      # BoxLang native
box install cborm       # Hibernate-based
box install quick       # Lightweight entities
```

📖 **Full documentation:** [bxorm.ortusbooks.com](https://bxorm.ortusbooks.com) · [cborm.ortusbooks.com](https://cborm.ortusbooks.com) · [quick.ortusbooks.com](https://quick.ortusbooks.com)

### Quick Example (Quick)

{% tabs %}
{% tab title="BoxLang" %}
```javascript
// models/User.bx
class extends="quick.models.BaseEntity" {

    property name="email"
    property name="name"

}
```
{% endtab %}
{% tab title="CFML" %}
```javascript
// models/User.cfc
component extends="quick.models.BaseEntity" {

    property name="email"
    property name="name"

}
```
{% endtab %}
{% endtabs %}

```javascript
// In a handler or service
property name="userService" inject="quickService:User";

users = userService.where( "active", true ).orderBy( "name" ).get()
user  = userService.findOrFail( rc.id )
```

## qb — Fluent Query Builder

When an ORM is more than you need, **qb** gives you expressive, injection-safe SQL:

```bash
box install qb
```

```javascript
property name="query" inject="QueryBuilder@qb";

var activeUsers = query.from( "users" )
    .where( "active", true )
    .orderBy( "created", "desc" )
    .limit( 20 )
    .get()
```

📖 **Full documentation:** [qb.ortusbooks.com](https://qb.ortusbooks.com)

## cfmigrations — Versioned Schema Changes

```bash
box install cfmigrations
```

```bash
# Create and run migrations
migrate create create_users_table
migrate up
```

Migrations keep schema changes in source control and apply them consistently across environments — the same files power [CbGenesis](https://cbgenesis.coldbox.org), our production starter template.

📖 **Full documentation:** [cfmigrations.ortusbooks.com](https://cfmigrations.ortusbooks.com)

## See Also

- [Serialization](serialization.md) — turning entities into API responses
- [Models](../the-basics/models/README.md) — the service layer and dependency injection
- [Testing Tools](README.md#developer-tools) — `cbMockData` for seeding test data
