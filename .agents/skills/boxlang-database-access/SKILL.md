---
name: boxlang-database-access
description: "Use this skill when writing BoxLang database code: queryExecute, bx:query, datasource configuration, parameterized queries, transactions, stored procedures, query manipulation, or preventing SQL injection."
---

# BoxLang Database Access

## Overview

BoxLang provides first-class database access via JDBC. Datasources are configured
in `boxlang.json` or `Application.bx`. Queries use `queryExecute()` (script) or
`bx:query` (tag syntax) with built-in parameterization to prevent SQL injection.

## Datasource Configuration

### In `boxlang.json`

```json
{
    "datasources": {
        "mainDB": {
            "driver": "mysql",
            "host": "${DB_HOST:localhost}",
            "port": "${DB_PORT:3306}",
            "database": "${DB_NAME:myapp}",
            "username": "${DB_USER}",
            "password": "${DB_PASS}",
            "options": {
                "maximumPoolSize": 10,
                "minimumIdle": 2,
                "connectionTimeout": 30000
            }
        },
        "readReplica": {
            "driver": "mysql",
            "host": "replica.example.com",
            "port": 3306,
            "database": "myapp",
            "username": "${DB_READ_USER}",
            "password": "${DB_READ_PASS}"
        }
    }
}
```

### In `Application.bx`

```boxlang
class {
    // Default datasource
    this.datasource = "mainDB"

    // Define datasources in application scope
    this.datasources = {
        mainDB: {
            driver   : "mysql",
            host     : server.system.environment.DB_HOST ?: "localhost",
            port     : 3306,
            database : "myapp",
            username : server.system.environment.DB_USER,
            password : server.system.environment.DB_PASS
        }
    }
}
```

## Basic Queries

### `queryExecute()` — Script Style (Preferred)

```boxlang
// SELECT — returns a query object
var users = queryExecute(
    "SELECT id, name, email, created_at FROM users WHERE status = :status ORDER BY name",
    { status: "active" },
    { datasource: "mainDB" }
)

// Iterate results
for ( var user in users ) {
    writeOutput( "#user.name# — #user.email#<br>" )
}

// Access by column name
var count = users.recordCount
var firstUser = users.name[1]   // 1-indexed

// Convert to array of structs
var userArray = users.toArray()
```

### `bx:query` — Tag Style

```boxlang
bx:query name="users" datasource="mainDB" {
    writeOutput( "SELECT id, name, email FROM users" )
    writeOutput( " WHERE status = " )
    bx:queryparam value="active" cfsqltype="cf_sql_varchar"
    writeOutput( " ORDER BY name" )
}
```

## Parameterized Queries (SQL Injection Prevention)

**Always use parameters** — never interpolate variables directly into SQL:

```boxlang
// CORRECT — parameterized
var user = queryExecute(
    "SELECT * FROM users WHERE email = :email",
    { email: form.email }
)

// CORRECT — with explicit type
var result = queryExecute(
    "SELECT * FROM orders WHERE user_id = :userId AND total > :minAmount",
    {
        userId    : { value: session.userId, cfsqltype: "cf_sql_integer" },
        minAmount : { value: 100.00,         cfsqltype: "cf_sql_decimal" }
    }
)

// WRONG — never do this (SQL injection risk)
var result = queryExecute( "SELECT * FROM users WHERE name = '#form.name#'" )
```

### Common SQL Types

| BoxLang Type | SQL Type |
|---|---|
| `cf_sql_integer` | INT |
| `cf_sql_bigint` | BIGINT |
| `cf_sql_decimal` | DECIMAL/NUMERIC |
| `cf_sql_varchar` | VARCHAR |
| `cf_sql_char` | CHAR |
| `cf_sql_longvarchar` | TEXT |
| `cf_sql_date` | DATE |
| `cf_sql_timestamp` | DATETIME/TIMESTAMP |
| `cf_sql_boolean` | BOOLEAN/TINYINT |
| `cf_sql_blob` | BLOB/BYTEA |

## INSERT, UPDATE, DELETE

```boxlang
// INSERT
var result = queryExecute(
    "INSERT INTO users (name, email, status, created_at) VALUES (:name, :email, :status, :now)",
    {
        name   : arguments.name,
        email  : arguments.email,
        status : "active",
        now    : { value: now(), cfsqltype: "cf_sql_timestamp" }
    },
    { result: "insertResult", datasource: "mainDB" }
)
var newId = insertResult.generatedKey

// UPDATE
queryExecute(
    "UPDATE users SET name = :name, updated_at = :now WHERE id = :id",
    {
        name : arguments.name,
        now  : { value: now(), cfsqltype: "cf_sql_timestamp" },
        id   : { value: arguments.id, cfsqltype: "cf_sql_integer" }
    }
)

// DELETE
queryExecute(
    "DELETE FROM users WHERE id = :id AND status = :status",
    {
        id     : { value: arguments.id, cfsqltype: "cf_sql_integer" },
        status : "inactive"
    }
)
```

## Query Results Manipulation

```boxlang
// Record count
var total = users.recordCount

// Column list
var cols = users.columnList   // "id,name,email"

// Access specific row/column
var firstName = users.name[1]   // row 1, column "name"

// Convert to array of structs
var userList = users.toArray()
userList.each( (row) -> processUser( row ) )

// Filter a query result
var activeUsers = queryFilter( users, (row) -> row.status == "active" )

// Sort a query result
var sorted = querySort( users, (a, b) -> a.name.compare( b.name ) )

// Create a query programmatically
var q = queryNew( "id,name,email", "integer,varchar,varchar" )
queryAddRow( q, { id: 1, name: "Ada", email: "ada@example.com" } )
queryAddRow( q, { id: 2, name: "Grace", email: "grace@example.com" } )

// Execute a function on each row
queryEach( users, (row) -> {
    sendEmail( row.email )
})

// Map query to array
var names = queryMap( users, (row) -> row.name.uCase() )
```

## Transactions

```boxlang
// Wraps all queries in a single atomic transaction
bx:transaction {
    queryExecute(
        "INSERT INTO orders (user_id, total) VALUES (:userId, :total)",
        { userId: session.userId, total: cartTotal }
    )

    var orderId = queryExecute(
        "SELECT LAST_INSERT_ID() AS id"
    ).id[1]

    cart.items.each( (item) -> {
        queryExecute(
            "INSERT INTO order_items (order_id, product_id, qty, price) VALUES (:orderId, :productId, :qty, :price)",
            { orderId: orderId, productId: item.id, qty: item.qty, price: item.price }
        )
    })

    // Explicit commit (optional — auto-commits at end of block)
    bx:transaction action="commit"
}

// Rollback on error
try {
    bx:transaction {
        performTransfer( fromId, toId, amount )
    }
} catch ( any e ) {
    bx:transaction action="rollback"
    throw( e )
}
```

## Stored Procedures

```boxlang
bx:storedproc procedure="sp_GetUserStats" datasource="mainDB" {
    bx:procparam type="in"  cfsqltype="cf_sql_integer" value=userId
    bx:procparam type="in"  cfsqltype="cf_sql_date"    value=startDate
    bx:procparam type="out" cfsqltype="cf_sql_integer" variable="totalOrders"
    bx:procparam type="out" cfsqltype="cf_sql_decimal" variable="totalRevenue"
    bx:procresult name="orderList" resultset=1
}

writeOutput( "Total orders: #totalOrders#" )
writeOutput( "Revenue: #totalRevenue#" )
```

## Dynamic Query Building (Safe Pattern)

```boxlang
function buildUserSearch( struct filters={} ) {
    var sql    = "SELECT id, name, email FROM users WHERE 1=1"
    var params = {}
    var i      = 1

    if ( filters.keyExists( "status" ) && len( filters.status ) ) {
        sql &= " AND status = :status"
        params.status = filters.status
    }

    if ( filters.keyExists( "role" ) && len( filters.role ) ) {
        sql &= " AND role = :role"
        params.role = filters.role
    }

    if ( filters.keyExists( "search" ) && len( trim( filters.search ) ) ) {
        sql &= " AND (name LIKE :search OR email LIKE :search)"
        params.search = "%" & trim( filters.search ) & "%"
    }

    sql &= " ORDER BY name LIMIT :limit OFFSET :offset"
    params.limit  = filters.limit  ?: 20
    params.offset = filters.offset ?: 0

    return queryExecute( sql, params )
}
```

## Query Caching

```boxlang
// Cache query results for 5 minutes
var products = queryExecute(
    "SELECT * FROM products WHERE active = 1",
    {},
    { cachedWithin: createTimeSpan( 0, 0, 5, 0 ) }
)
```

## Multiple Datasources

```boxlang
// Override datasource per query
var legacyData = queryExecute(
    "SELECT * FROM legacy_users",
    {},
    { datasource: "legacyDB" }
)

var analyticsData = queryExecute(
    "SELECT * FROM events WHERE date > :since",
    { since: dateAdd( "d", -30, now() ) },
    { datasource: "analyticsDB" }
)
```

## Query of Queries (QoQ)

Run SQL against in-memory query objects without touching the database by passing
`{ dbtype: "query" }` as the options argument. Local query variables become table
sources.

### Basic QoQ

```boxlang
// Load data from the database once
var users = queryExecute( "SELECT * FROM users" )

// Now query the in-memory result — no DB roundtrip
var activeUsers = queryExecute(
    "SELECT * FROM users WHERE active = :active ORDER BY lastName",
    { active: true },
    { dbtype: "query" }
)
```

### JOIN Across Multiple In-Memory Queries

```boxlang
var users  = queryExecute( "SELECT * FROM users" )
var orders = queryExecute( "SELECT * FROM orders" )

// Join two in-memory queries
var report = queryExecute(
    "
        SELECT u.firstName, u.lastName, COUNT(o.id) AS orderCount
        FROM users u
        LEFT JOIN orders o ON u.id = o.userId
        GROUP BY u.firstName, u.lastName
        ORDER BY orderCount DESC
    ",
    {},
    { dbtype: "query" }
)
```

### QoQ Aggregates

```boxlang
var orders = queryExecute( "SELECT * FROM orders" )

var summary = queryExecute(
    "
        SELECT
            COUNT(*)   AS totalOrders,
            SUM(total) AS totalRevenue,
            AVG(total) AS avgValue,
            MIN(total) AS minOrder,
            MAX(total) AS maxOrder
        FROM orders
    ",
    {},
    { dbtype: "query" }
)
writeOutput( "Revenue: #summary.totalRevenue#" )
```

### Complex Filtering Without Extra DB Calls

```boxlang
var allProducts = queryExecute( "SELECT * FROM products" )

// Apply user-controlled search in memory — safe from SQL injection
// because we're not hitting the DB, and params are still typed
var filtered = queryExecute(
    "SELECT * FROM products WHERE category = :cat AND price < :maxPrice ORDER BY name",
    { cat: url.category, maxPrice: url.maxPrice },
    { dbtype: "query" }
)
```

**When to use QoQ:**
- Filtering or sorting an already-loaded dataset without another DB round-trip
- Joining two result sets from different datasources
- Applying aggregate functions (COUNT, SUM, AVG) on in-memory data
- Paging through results that are already in memory

## References

- [Database / JDBC](https://boxlang.ortusbooks.com/boxlang-framework/database)
- [QueryExecute](https://boxlang.ortusbooks.com/boxlang-language/reference/built-in-functions/query/queryexecute)
- [Datasource Configuration](https://boxlang.ortusbooks.com/getting-started/configuration#datasources)
- [SQL Injection Prevention](https://owasp.org/www-community/attacks/SQL_Injection)