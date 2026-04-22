---
name: boxlang-best-practices
description: "Use this skill when writing, reviewing, or improving BoxLang code to ensure it follows community best practices for naming, structure, scoping, error handling, performance, and maintainability."
---

# BoxLang Best Practices

## Overview

BoxLang is a modern dynamic JVM language. These best practices reflect idiomatic
BoxLang patterns informed by the language's design, CFML heritage, and JVM
performance characteristics. Following them produces code that is readable,
maintainable, performant, and safe.

---

## Naming Conventions

| Item | Convention | Example |
|------|-----------|---------|
| Variables | camelCase | `userProfile`, `orderTotal` |
| Functions/Methods | camelCase | `getUserById()`, `processOrder()` |
| Classes | PascalCase | `UserService`, `OrderProcessor` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |
| Files (classes) | PascalCase | `UserService.bx` |
| Files (templates) | camelCase or kebab-case | `userProfile.bxm`, `order-details.bxm` |
| Files (scripts) | camelCase | `buildReport.bxs` |

```boxlang
// Good
class UserService {
    function getUserById( required numeric id ) {
        var MAX_RETRIES = 3
        var userId = arguments.id
        return userRepository.find( userId )
    }
}
```

---

## Variable Scoping

Always declare local variables with `var` inside functions to avoid polluting
the `variables` scope (the component-level scope).

```boxlang
// BAD — leaks into variables scope
function process() {
    result = doWork()
    return result
}

// GOOD — properly local
function process() {
    var result = doWork()
    return result
}
```

Use explicit scope prefixes when ambiguity exists:

```boxlang
function getUser( required numeric id ) {
    // Explicitly scope to avoid confusion
    var userData = variables.userRepo.find( arguments.id )
    return userData
}
```

### Scope Lookup Performance

BoxLang walks the scope chain on each unscoped variable access. In hot code
paths (tight loops, high-traffic request handlers), scope your variables
explicitly for predictable performance:

```boxlang
// GOOD in hot paths — no scope chain walk
var len = arguments.items.len()
for ( var i = 1; i <= len; i++ ) {
    // process arguments.items[ i ]
}
```

---

## Functions

### Always Declare Argument Types and Required Status

```boxlang
// BAD — no type information
function processOrder( order, userId ) { ... }

// GOOD — self-documenting, validated at runtime
function processOrder( required struct order, required numeric userId ) {
    ...
}
```

### Use Named Arguments for Clarity

```boxlang
// Hard to read
createUser( "John", "Doe", "john@example.com", true )

// GOOD — named arguments document intent
createUser(
    firstName = "John",
    lastName  = "Doe",
    email     = "john@example.com",
    active    = true
)
```

### Return Types

Declare return types for public functions to document contracts and enable
better IDE support:

```boxlang
struct function getUser( required numeric id ) {
    return userService.find( arguments.id )
}

array function listActiveUsers() {
    return userService.findByStatus( "active" )
}
```

---

## Error Handling

### Catch Specific Exception Types

```boxlang
// BAD — catches everything, hides bugs
try {
    processOrder( order )
} catch ( any e ) {
    logError( e )
}

// GOOD — handle specific cases, re-throw unknown
try {
    processOrder( order )
} catch ( "Database" e ) {
    handleDatabaseError( e )
} catch ( "Validation" e ) {
    return { success: false, message: e.message }
} catch ( any e ) {
    // Re-throw unexpected errors
    rethrow
}
```

### Use `cffinally` for Cleanup

```boxlang
transaction {
    try {
        updateOrder( order )
        chargePayment( payment )
        transactionCommit()
    } catch ( any e ) {
        transactionRollback()
        rethrow
    }
}
```

---

## Null Safety

Use the safe-navigation operator (`?.`) and Elvis operator (`?:`) to
avoid null pointer errors:

```boxlang
// Null-safe chained access
var city = user?.address?.city ?: "Unknown"

// Null-safe method calls
var count = order?.items?.len() ?: 0
```

Prefer `isNull()` over direct comparisons with `null`:

```boxlang
if ( isNull( result ) ) {
    return getDefault()
}
```

---

## Closures vs Lambdas

Use **lambdas** (`->`) for pure deterministic operations on their arguments only.
Use **closures** (`=>`) when accessing outer scope variables or calling external
functions/BIFs.

```boxlang
// Lambda — only uses the item argument (pure transform)
var doubled = numbers.map( ( n ) -> n * 2 )

// Closure — accesses outer variable `threshold`
var filtered = numbers.filter( ( n ) => n > threshold )

// Closure — calls external BIF
var upper = words.map( ( w ) => uCase( w ) )
```

---

## Struct and Array Literals

Prefer literal syntax over constructor functions:

```boxlang
// GOOD — literal syntax
var user = {
    name:  "Alice",
    email: "alice@example.com",
    roles: [ "admin", "user" ]
}

// Avoid unless dynamic keys are needed
var user = structNew()
user.name = "Alice"
```

Use ordered struct literal syntax when key order matters:

```boxlang
// Ordered struct (insertion order preserved)
var config = [=
    host: "localhost",
    port: 5432,
    database: "myapp"
=]
```

---

## String Interpolation

Use `#expression#` for interpolation in strings and templates. For complex
expressions, assign to a variable first for readability:

```boxlang
// Simple interpolation
var message = "Hello, #user.name#!"

// Complex — extract first
var formattedDate = dateTimeFormat( now(), "long" )
var header = "Report generated on #formattedDate#"
```

---

## Component (Class) Design

### Constructor Pattern

Use `init()` as the constructor. Return `this` for fluent construction:

```boxlang
class UserService {

    property name="userRepo" inject="UserRepository"

    function init( required UserRepository userRepository ) {
        variables.userRepo = arguments.userRepository
        return this
    }

}
```

### Keep Classes Focused (Single Responsibility)

Each `.bx` class should have one primary purpose. Avoid "god objects" that
handle unrelated concerns. Split into service, repository, and model layers.

---

## Performance Tips

1. **Cache expensive lookups** — store results in `application` scope for
   shared read-only data; invalidate on change.
2. **Use `trustedCache=true` in production** — prevents disk I/O on class
   file checks.
3. **Pre-compute in constructors** — if a value won't change, compute it once
   during instantiation.
4. **Prefer `each()` / `map()` / `filter()` over manual loops** for
   collection work — more readable and JIT-friendly.
5. **Use virtual threads** (`runAsync` defaults) for I/O-bound async work;
   use fixed-pool executors for CPU-bound work.

---

## Code Organization

```
/app
  /models           -- Business domain classes (.bx)
  /services         -- Service layer classes (.bx)
  /repositories     -- Data access classes (.bx)
  /handlers         -- Request handlers / controllers (.bx)
  /views            -- Templates (.bxm)
  /includes         -- Reusable partial templates (.bxm)
  /scripts          -- Standalone scripts (.bxs)
  Application.bx    -- Application lifecycle
```

---

## Common Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|-------------|---------|-----|
| Unscoped vars in functions | Variables bleed into component scope | Always use `var` |
| Silent catch-all `catch(any)` | Swallows unexpected errors | Re-throw unknown exceptions |
| Logic in templates | Hard to test, poor separation | Move to services/handlers |
| Direct SQL in handlers | No reuse, SQL injection risk | Use repository classes with parameterized queries |
| Storing secrets in code | Security risk | Use environment variables via `${env.VAR_NAME}` in config |
| Overusing `application` scope | Concurrency bugs | Use proper locking (`bx:lock`) for writes |
| `arr[ 0 ]` (zero-based index) | ArrayIndexOutOfBoundsException | BoxLang arrays are **1-indexed**: use `arr[ 1 ]` or `arr.first()` |
| Trailing `;` on statements | Noisy / inconsistent style | Semicolons are optional on statements — omit them |
| `cfheader()` / `<cfabort>` | CFML syntax, not BoxLang | Use `bx:header`, `bx:abort`, etc. |
| Named args on Java objects | Not supported, throws runtime error | Always use positional arguments for Java method calls |
| `createObject("java","path")` per call | Verbose, repeated boilerplate | `import java:fully.qualified.Class` once, then use the class name directly |

---

## BoxLang vs CFML Quick-Reference

BoxLang evolved from CFML but uses different syntax for many constructs. Do NOT
use `cf`-prefixed tags or functions in BoxLang source files.

| CFML | BoxLang Equivalent |
|------|--------------------|
| `cfheader(name="X", value="Y")` | `bx:header name="X" value="Y";` |
| `cflocation(url="...")` | `bx:location url="...";` |
| `cfabort` | `bx:abort;` |
| `cfparam name="x" default=""` | `bx:param name="x" default="";` |
| `cfinclude template="f.cfm"` | `bx:include template="f.bxm";` |
| `<cfsilent>` | `bx:silent { ... }` |
| `createObject("java","path.Class")` | `import java:path.Class` then `new java:path.Class()` |

---

## Array Best Practices

BoxLang arrays are **1-indexed**. This is a common source of bugs for developers
coming from Java/JavaScript backgrounds.

```boxlang
var items = [ "a", "b", "c" ]

// CORRECT
var first = items[ 1 ]          // "a"
var last  = items[ items.len() ] // "c"
var first = items.first()        // preferred — more readable
var last  = items.last()         // preferred

// WRONG (throws ArrayIndexOutOfBoundsException)
var first = items[ 0 ]

// Looping — i starts at 1
for ( var i = 1; i <= items.len(); i++ ) {
    process( items[ i ] )
}
```

### Passing Single Values to Java Varargs

Java varargs methods require a BoxLang **array**, not a bare scalar value:

```boxlang
// CORRECT — wrap in array
storage.query( "SELECT * FROM t WHERE id = ?", [ requestId ] )

// WRONG — bare value is not accepted by Java varargs
storage.query( "SELECT * FROM t WHERE id = ?", requestId )    // throws
```