---
name: boxlang-code-reviewer
description: "Use this skill when reviewing BoxLang code for quality, correctness, security vulnerabilities, performance issues, style violations, or when providing structured code review feedback following BoxLang best practices and security guidelines."
---

# BoxLang Code Reviewer

## Overview

This skill provides a structured checklist and framework for reviewing BoxLang
code. Apply these checks systematically when reviewing PRs, auditing existing
code, or self-reviewing before committing.

---

## Review Framework

When reviewing BoxLang code, evaluate these categories in order of priority:

1. **Security** — Vulnerabilities that could be exploited
2. **Correctness** — Logic errors, edge cases, null safety
3. **Performance** — Inefficiencies, unnecessary work
4. **Maintainability** — Readability, naming, structure
5. **Style** — Conventions, consistency

---

## Security Checks

### SQL Injection

```boxlang
// RED FLAG — string interpolation in SQL
queryExecute( "SELECT * FROM users WHERE id = #url.id#" )

// REQUIRED FIX — always parameterize
queryExecute(
    "SELECT * FROM users WHERE id = :id",
    { id: { value: url.id, cfsqltype: "cf_sql_integer" } }
)
```

**Review question:** Is every SQL value passed via `queryParam` / `:name` binding?

### XSS (Cross-Site Scripting)

```boxlang
// RED FLAG — raw user input rendered in HTML
<bx:output>#form.comment#</bx:output>

// REQUIRED FIX — encode for context
<bx:output>#encodeForHTML( form.comment )#</bx:output>
```

**Review question:** Is every user-supplied value encoded with the appropriate
`encodeFor*()` function before output?

### File Upload Validation

- Is the file extension validated against an allowlist?
- Is MIME type validated server-side (not just by browser)?
- Are files stored **outside the webroot**?
- Is file size limited?

### Path Traversal

**Review question:** Does any file read/write accept a user-supplied path?
If yes, is it validated with canonical path comparison?

### Secrets in Code

**RED FLAG:** API keys, passwords, tokens hardcoded in `.bx`, `.bxs`, `.bxm`,
or `boxlang.json`.

**REQUIRED FIX:** Use `${env.SECRET_NAME}` in config, and access via
`server.system.environment.SECRET_NAME` in code.

### Remote Function Exposure

**Review question:** Are `remote` functions authenticated before executing?
Are all arguments validated before use?

---

## Correctness Checks

### Variable Scoping

```boxlang
// RED FLAG — missing var keyword (bleeds into variables scope)
function process() {
    result = loadData()    // BAD
    return result
}

// CORRECT
function process() {
    var result = loadData()
    return result
}
```

### Null Safety

```boxlang
// RED FLAG — potential null pointer
var name = user.profile.displayName   // throws if profile is null

// CORRECT — null-safe navigation
var name = user?.profile?.displayName ?: "Anonymous"
```

### Exception Handling

```boxlang
// RED FLAG — catch-all silently swallows bugs
try {
    doWork()
} catch ( any e ) {
    logError( e )   // continues execution after unexpected error
}

// CORRECT — re-throw unknown errors
try {
    doWork()
} catch ( "ExpectedError" e ) {
    handleExpected( e )
} catch ( any e ) {
    logError( e )
    rethrow   // let unexpected errors propagate
}
```

### Edge Cases to Check

- What happens when the input is empty / null / zero?
- What happens at collection boundary (first and last element)?
- What happens when a database/API call returns no rows?
- Are all required struct keys guarded with `structKeyExists()`?

---

## Performance Checks

### Scope Access in Loops

```boxlang
// SLOWER — scope chain walked every iteration
for ( var i = 1; i <= items.len(); i++ ) {
    process( items[ i ] )
}

// FASTER — len() called once
var count = items.len()
for ( var i = 1; i <= count; i++ ) {
    process( items[ i ] )
}
```

### N+1 Query Pattern

```boxlang
// RED FLAG — query inside a loop (N+1 problem)
for ( var order in orders ) {
    order.customer = queryExecute( "SELECT * FROM customers WHERE id = #order.customerId#" )
}

// CORRECT — fetch all in one query with JOIN or batch lookup
var enriched = queryExecute(
    "SELECT o.*, c.name as customerName
     FROM orders o
     JOIN customers c ON c.id = o.customerId
     WHERE o.status = :status",
    { status: { value: "active", cfsqltype: "cf_sql_varchar" } }
)
```

### Application Scope Cache Writes

```boxlang
// RED FLAG — unguarded write to application scope (race condition)
application.settings = loadSettings()

// CORRECT — use locking
bx:lock name="app-settings-lock" type="exclusive" timeout="5" {
    application.settings = loadSettings()
}
```

---

## Maintainability Checks

### Naming

| Check | Bad Example | Good Example |
|-------|------------|-------------|
| Variables clear | `var x = getData()` | `var userProfile = getProfile( id )` |
| Functions descriptive | `function do()` | `function processPayment()` |
| Boolean names readable | `var flag = check()` | `var isEligible = checkEligibility()` |
| Magic numbers named | `if ( status == 3 )` | `if ( status == STATUS_SUSPENDED )` |

### Function Length

Flag functions exceeding ~50 lines — they likely need splitting. Each function
should do one thing clearly.

### Argument Declarations

```boxlang
// RED FLAG — no argument metadata
function getUser( id, options ) { ... }

// REQUIRED — typed, required annotations
function getUser( required numeric id, struct options = {} ) { ... }
```

### Dead Code

Flag:
- Variables declared but never read
- Functions defined but never called
- Commented-out blocks left in production code
- Conditions that can never be true

---

## Style Checks

### Semicolons

BoxLang docs state: **do not use semicolons** except where required.

```boxlang
// BAD — unnecessary semicolons
var name = "BoxLang";
return result;

// CORRECT — no semicolons needed
var name = "BoxLang"
return result

// OK — semicolons required here
property name="userId" type="numeric";
```

### Closures vs Lambdas

```boxlang
// Correct — lambda for pure transforms (no outer scope)
var doubled = numbers.map( ( n ) -> n * 2 )

// Correct — closure for outer scope access or BIF calls
var filtered = numbers.filter( ( n ) => n > minValue )
var upper    = words.map( ( w ) => uCase( w ) )
```

### Struct Literals

```boxlang
// Acceptable but verbose
var user = structNew()
user.name = "Alice"

// Preferred — literal syntax
var user = { name: "Alice", email: "alice@example.com" }
```

---

## Review Output Template

When writing review feedback, use this structure:

```
## Critical (must fix before merge)

- [ ] **[Security/SQL Injection]** Line 42: `url.id` is interpolated directly into SQL.
      Fix: use `:id` binding with `cfsqltype: "cf_sql_integer"`.

- [ ] **[Security/XSS]** Line 87: `form.description` rendered without encoding.
      Fix: `encodeForHTML( form.description )`.

## Major (should fix)

- [ ] **[Correctness/Null Safety]** Line 23: `user.address.city` — `address` may be null.
      Fix: use `user?.address?.city ?: "Unknown"`.

- [ ] **[Performance/N+1]** Lines 55–60: query inside loop will execute N queries.
      Fix: use JOIN or batch lookup.

## Minor (consider fixing)

- [ ] **[Style/Naming]** `var x` on line 31 — rename to clarify purpose.
- [ ] **[Docs]** `processOrder()` lacks `@param` and `@return` documentation.

## Positive Feedback

- Good use of parameterized queries in `getUserById()`.
- Error handling in `chargePayment()` correctly re-throws unknown exceptions.
```

---

## Automated Tools

Run these before manual review:

```bash
# Format check (BoxLang code style)
box run-script format:check

# Run test suite
./run --reporter=text

# Check for outdated modules (dependency audit)
box outdated

# Security scan (if using CommandBox security module)
box security:scan
```