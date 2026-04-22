---
name: ortus-coding-standards
description: "Use this skill when writing, reviewing, or formatting any Ortus Solutions code (BoxLang, CFML, or Java) to ensure it follows the official Ortus coding standards: indentation, spacing, brace placement, naming, alignment, comments, and structural conventions."
---

# Ortus Coding Standards

## Overview

Ortus Solutions maintains consistent formatting rules across all BoxLang, CFML, and Java
source code. These rules are enforced via the **OrtusJava Eclipse formatter profile**
(`ortus-java-style.xml`) for Java, and via the conventions documented here for BoxLang
and CFML. When in doubt, match what already exists in the file you are editing.

---

## Indentation

Use **tabs** for indentation — never spaces for the leading indent.

- Class body: indented one tab from the class declaration.
- Method body: indented one tab from the method signature.
- Switch cases: indented one tab from `switch`.
- Case body: indented one tab from the `case` label.
- Continuation lines (wrapped expressions): indented one level.

```java
// Java — tab-indented, end-of-line braces
public class UserService {

    public User findById( int id ) {
        if ( id <= 0 ) {
            throw new IllegalArgumentException( "id must be positive" );
        }
        return repository.find( id );
    }

}
```

```js
// BoxLang — same indentation rules
class UserService {

    function findById( required numeric id ) {
        if ( id <= 0 ) {
            throw( type = "InvalidArgument", message = "id must be positive" )
        }
        return variables.repository.find( arguments.id )
    }

}
```

---

## Spacing Inside Parentheses

**Always** place a space after `(` and before `)` in:

- Method declarations
- Method invocations
- Control flow keywords (`if`, `for`, `while`, `switch`, `catch`, `synchronized`)
- Casts
- Parenthesized expressions

```java
// Java
public void process( String name, int count ) {
    if ( count > 0 ) {
        doThing( name );
    }
    var result = ( String ) rawValue;
}
```

```js
// BoxLang
function process( required string name, required numeric count ) {
    if ( count > 0 ) {
        doThing( name )
    }
}
```

**Never** place a space between a function name/keyword and its opening `(`:

```java
// WRONG
doThing ( name );

// RIGHT
doThing( name );
```

**No** space inside empty parentheses:

```java
// RIGHT
init()
list()
```

---

## Brace Style

Use **K&R / end-of-line** brace placement — opening brace on the same line as
the statement, not on its own line.

```java
// RIGHT
public void save( User user ) {
    if ( user.isValid() ) {
        repository.save( user );
    } else {
        throw new ValidationException( "invalid user" );
    }
}

// WRONG — Allman style, do not use
public void save( User user )
{
    ...
}
```

The same rule applies to:

- Class declarations
- Method declarations
- `if` / `else` / `else if`
- `for` / `while` / `do-while`
- `try` / `catch` / `finally`
- `switch`
- Array initializers

---

## Always Use Braces

Even for single-statement bodies, always use braces. Never place an `if` or loop
body on the same line as its condition.

```java
// WRONG — no braces, body on same line
if ( user == null ) return;

for ( var item : items ) process( item );

// RIGHT
if ( user == null ) {
    return;
}

for ( var item : items ) {
    process( item );
}
```

---

## Spacing Around Operators

Place **one space** on each side of:

- Assignment operators (`=`, `+=`, `-=`, etc.)
- Comparison operators (`==`, `!=`, `<`, `>`, `<=`, `>=`)
- Arithmetic operators (`+`, `-`, `*`, `/`, `%`)
- Bitwise operators (`&`, `|`, `^`, `~`)
- Shift operators (`<<`, `>>`, `>>>`)
- String concatenation (`&` in BoxLang/CFML, `+` in Java)
- Lambda arrows (`->` in Java, `->` / `=>` in BoxLang)
- Ternary operator (`?` and `:`)

```java
// Java
var total  = price * quantity;
var result = isValid ? "yes" : "no";
var upper  = items.stream().map( s -> s.toUpperCase() ).collect( Collectors.toList() );
```

```js
// BoxLang
var total  = price * quantity
var result = isValid ? "yes" : "no"
var upper  = items.map( ( s ) -> uCase( s ) )
```

**No** space for:

- Unary operators before their operand: `!condition`, `++i`, `-value`
- After `!`: `!isValid` (no space between `!` and `isValid`)
- Ellipsis: `String... values` (no space before `...`)

---

## Column Alignment

When multiple related declarations or assignments appear in a block, **align on
columns** so they read like a table. This applies to:

- Field (property) declarations
- Local variable declarations
- Assignment groups

```java
// Java — aligned field declarations
private final UserRepository  userRepository;
private final EmailService    emailService;
private final int             maxRetries;
```

```js
// BoxLang — aligned assignments
var firstName = "Alice"
var lastName  = "Smith"
var email     = "alice@example.com"
var active    = true

this.name                 = "My App"
this.sessionManagement    = true
this.sessionTimeout       = createTimespan( 0, 1, 0, 0 )
this.timezone             = "UTC"
```

Apply alignment within logical groups (separated by blank lines). Do not force
alignment across unrelated declarations.

---

## Blank Lines

| Context | Rule |
| --- | --- |
| Between methods | 1 blank line |
| Between abstract methods | 1 blank line |
| Between fields | 0 blank lines (fields are grouped together) |
| After `package` declaration | 1 blank line |
| First declaration inside a class body | 1 blank line |
| Beginning of a method body | 0 blank lines |
| End of a code block | 0 blank lines |
| Before a code block (e.g., before `{`) | 0 blank lines |

```java
// Java — correct blank line usage
public class OrderService {

    private final OrderRepository orderRepository;
    private final PaymentService  paymentService;

    public OrderService( OrderRepository orderRepository, PaymentService paymentService ) {
        this.orderRepository = orderRepository;
        this.paymentService  = paymentService;
    }

    public Order create( CreateOrderRequest request ) {
        var order = buildOrder( request );
        orderRepository.save( order );
        return order;
    }

    private Order buildOrder( CreateOrderRequest request ) {
        return new Order( request.getItems(), request.getUserId() );
    }

}
```

---

## Naming Conventions

| Item | Convention | Example |
| --- | --- | --- |
| Java classes | `PascalCase` | `UserService`, `OrderProcessor` |
| Java interfaces | `PascalCase` | `UserRepository`, `Serializable` |
| Java methods | `camelCase` | `getUserById()`, `processOrder()` |
| Java fields | `camelCase` | `userRepository`, `maxRetries` |
| Java constants | `UPPER_SNAKE_CASE` | `MAX_RETRIES`, `DEFAULT_TIMEOUT` |
| Java packages | `lowercase.dot.separated` | `ortus.boxlang.runtime.services` |
| BoxLang classes | `PascalCase` | `UserService.bx`, `OrderProcessor.bx` |
| BoxLang functions | `camelCase` | `getUserById()`, `processOrder()` |
| BoxLang variables | `camelCase` | `userProfile`, `orderTotal` |
| BoxLang templates | `camelCase` or `kebab-case` | `userProfile.bxm`, `order-detail.bxm` |
| BoxLang scripts | `camelCase` | `buildReport.bxs` |
| BoxLang constants (local) | `UPPER_SNAKE_CASE` | `var MAX_RETRIES = 3` |

---

## Comments and Javadoc

### Javadoc (Java)

Write Javadoc for all public classes and public/protected methods.

```java
/**
 * Finds a user by their unique identifier.
 *
 * @param  id  the user's primary key (must be positive)
 *
 * @return the matching {@link User}, or {@code null} if not found
 *
 * @throws IllegalArgumentException if {@code id} is not positive
 */
public User findById( int id ) {
    ...
}
```

Rules:
- Opening `/**` and closing `*/` on their own lines.
- One space after `*` on every body line.
- `@param`, `@return`, and `@throws` tags on separate lines.
- Do not join description lines — keep each sentence on its own line.
- `@param` descriptions aligned (not required but preferred for readability).

### Inline Comments (Java)

```java
// Use single-line comments for brief explanations above the line they describe.
// Never end-of-line comments for non-obvious things — put them above instead.

// Calculate total including tax
var total = basePrice * ( 1 + taxRate );
```

Do not leave commented-out code in committed files.

### BoxLang Documentation Comments

```js
/**
 * Finds a user by their unique identifier.
 *
 * @id         The user's primary key. Must be a positive integer.
 * @return     struct containing user data, or an empty struct if not found.
 */
struct function findById( required numeric id ) {
    ...
}
```

---

## BoxLang-Specific Conventions

### Semicolons

**Do not use semicolons** on regular statements in BoxLang. They are optional and
the standard is to omit them for cleaner code.

```js
// RIGHT — no semicolons
var result = calculate( x, y )
return result

// WRONG
var result = calculate( x, y );
return result;
```

**Exceptions where semicolons ARE required:**

- Property declarations: `property name="fieldName" type="string";`
- Multiple statements on a single line (avoid this pattern)

### Lambdas vs Closures

Use **lambdas** (`->`) when the function is deterministic and operates only on
its own arguments:

```js
// Lambda — only uses the item argument (no outer scope access)
var doubled = numbers.map( ( n ) -> n * 2 )
var upper   = words.map( ( w ) -> w.uCase() )
```

Use **closures** (`=>`) when the function accesses variables from the enclosing
scope or calls external BIFs:

```js
// Closure — accesses outer variable `threshold`
var filtered = numbers.filter( ( n ) => n > threshold )

// Closure — calls external BIF
var encoded = values.map( ( v ) => encodeForHTML( v ) )
```

### Argument Scope in Closures

Inside a closure, **do not use the `arguments` scope** to access outer function
parameters. The `arguments` scope inside the closure refers only to the
closure's own parameters:

```js
// WRONG — arguments.name refers to the closure's arguments, not the outer function's
function process( required string name ) {
    var found = items.filter( ( item ) => item.name != arguments.name )
}

// RIGHT — use unscoped name; BoxLang finds it in the enclosing scope
function process( required string name ) {
    var found = items.filter( ( item ) => item.name != name )
}
```

### Function Call Spacing

```js
// RIGHT — spaces inside parentheses, space after comma
doThing( arg1, arg2 )
createUser( firstName = "Alice", active = true )

// WRONG — no spaces inside parens
doThing(arg1, arg2)
```

### Struct and Array Literals

```js
// Preferred — literal syntax with alignment
var config = {
    host    : "localhost",
    port    : 5432,
    database: "myapp"
}

var roles = [ "admin", "editor", "viewer" ]
```

---

## Multi-Line Method Calls

When a call does not fit on one line, wrap arguments with one argument per line
and align them:

```java
// Java
var result = userService.createUser(
    request.getFirstName(),
    request.getLastName(),
    request.getEmail(),
    request.getRoleId()
);
```

```js
// BoxLang
var result = userService.createUser(
    firstName = request.firstName,
    lastName  = request.lastName,
    email     = request.email,
    roleId    = request.roleId
)
```

Wrap **before** operators, not after:

```java
// RIGHT — operator at start of wrapped line
var isEligible = user.isActive()
    && user.hasVerifiedEmail()
    && !user.isSuspended();
```

---

## Java-Specific Patterns

### Import Order

Group imports in this order with one blank line between groups:

1. `java.*` and `javax.*`
2. Third-party libraries
3. `ortus.boxlang.*` internal classes

Do not use wildcard imports (`import java.util.*`).

### Null Checks

Prefer `Objects.requireNonNull` in constructors and public entry points:

```java
public UserService( UserRepository repo ) {
    this.repo = Objects.requireNonNull( repo, "repo must not be null" );
}
```

### `final` Fields

Declare all injected dependencies and constructor-set fields as `final`:

```java
private final UserRepository userRepository;
private final EmailService   emailService;
```

### Avoid Magic Numbers

Extract literal numbers and strings into named constants:

```java
// WRONG
if ( retries > 3 ) { ... }

// RIGHT
private static final int MAX_RETRIES = 3;
if ( retries > MAX_RETRIES ) { ... }
```

---

## Common Anti-Patterns to Avoid

| Anti-Pattern | Fix |
| --- | --- |
| Allman-style braces | Use K&R (end-of-line) braces |
| No spaces inside `(` `)` in calls | Always `doThing( arg )` not `doThing(arg)` |
| Semicolons on BoxLang statements | Remove unless a property declaration |
| Unaligned field groups | Align with spaces/tabs to column |
| `arguments.name` inside closure accessing outer scope | Remove scope prefix |
| Comment-out dead code | Delete it; use version control |
| Wildcard imports in Java | Use explicit imports |
| Magic numbers | Extract to named constants |
| Mutable `public` fields in Java | Use `private final` + accessor |
| One-liner `if` without braces | Always use braces |