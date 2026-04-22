---
name: boxlang-language-fundamentals
description: "Use this skill when writing or reviewing BoxLang code covering syntax, file types, variables, scopes, operators, control flow, exception handling, type system, and modern language features like destructuring and spread syntax."
---

# BoxLang Language Fundamentals

## Overview

BoxLang is a modern, dynamic JVM language (JRE 21+) influenced by Java, CFML, Python,
Ruby, Go, and PHP. It compiles to Java bytecode and provides complete Java
interoperability with a more expressive, concise syntax.

## File Types

| Extension | Purpose |
|-----------|---------|
| `.bx` | Class files (components, services, models) |
| `.bxs` | Script files (standalone scripts, CLIs) |
| `.bxm` | Markup/template files (HTML output, views) |

## Variables and Scopes

BoxLang uses dynamic typing — types are inferred at runtime. Explicit type
annotations are optional but supported.

```boxlang
// Variable declaration (var keyword in functions)
var name = "BoxLang"
var count = 42
var price = 9.99
var active = true

// Null
var nothing = null
```

### Scope Chain (resolution order in a function)

1. `local` — variables declared with `var` inside a function
2. `arguments` — function parameters
3. `variables` — component/class private scope
4. `this` — component/class public scope
5. Named scopes: `url`, `form`, `session`, `application`, `request`, `cgi`, `server`

```boxlang
// Explicit scope reference
variables.counter = 0
this.publicValue = "visible"
local.tempResult = compute()
```

## Operators

```boxlang
// Arithmetic
a + b   a - b   a * b   a / b   a % b   a ^ b   a \ b  // integer divide

// Comparison
a == b   a != b   a > b   a < b   a >= b   a <= b
a === b  a !== b  // strict (type + value)

// Logical
a && b   a || b   !a
a and b  a or b   not a   // word operators

// String concatenation
a & b

// Safe-navigation (null-safe member access)
user?.address?.city   // returns null instead of throwing

// Inclusive range (v1.12+)
1..5   // produces [1, 2, 3, 4, 5] — both ends included

// Ternary
result = condition ? trueValue : falseValue

// Elvis (null coalescing)
result = value ?: "default"
```

## Control Flow

```boxlang
// if / else if / else
if ( age >= 18 ) {
    writeOutput( "adult" )
} else if ( age >= 13 ) {
    writeOutput( "teen" )
} else {
    writeOutput( "child" )
}

// switch
switch ( status ) {
    case "active":
        doActive()
        break
    case "pending":
    case "review":
        doPending()
        break
    default:
        doDefault()
}

// for (index)
for ( var i = 1; i <= 10; i++ ) {
    writeOutput( i )
}

// for-in (collection)
for ( var item in myArray ) {
    writeOutput( item )
}

// for-in with destructuring (v1.12+)
for ( var [key, value] in myStruct ) {
    writeOutput( "#key# = #value#" )
}

// while
while ( queue.len() > 0 ) {
    process( queue.dequeue() )
}

// do-while
do {
    attempt = tryConnect()
} while ( !attempt.success && retries++ < 3 )
```

## Exception Handling

```boxlang
try {
    result = riskyOperation()
} catch ( "CustomException" e ) {
    handleCustom( e )
} catch ( any e ) {
    // e.message, e.detail, e.stackTrace, e.type
    logError( e.message )
} finally {
    cleanup()
}

// Throwing exceptions
throw( message="Something went wrong", type="MyApp.ValidationError", detail="Field X is required" )

// Or as an object
throw new MyException( "Bad input" )
```

## Strings

```boxlang
// Double-quoted: interpolation enabled
var greeting = "Hello, #name#!"
var multi = "Line one
Line two"

// Single-quoted: literal (no interpolation)
var literal = 'Hello, #name#'   // outputs literally: Hello, #name#

// Common string functions
len( str )
trim( str )
uCase( str ) / lCase( str )
left( str, n ) / right( str, n ) / mid( str, start, n )
replace( str, search, replacement )
reFind( pattern, str )
listToArray( str, delimiter )
str.contains( "foo" )   // member function syntax
```

## Arrays

**CRITICAL: BoxLang arrays are 1-indexed (not 0-indexed like Java).**
The first element is always at index `1`.

```boxlang
var fruits = [ "apple", "banana", "cherry" ]

// Access by index — starts at 1
fruits[ 1 ]   // "apple"  ✅
fruits[ 0 ]   // null / out-of-bounds  ❌  WRONG

// Preferred: use named accessors instead of numeric index
fruits.first()   // "apple"   — first element
fruits.last()    // "cherry"  — last element

// Common array operations
fruits.len()               // 3
fruits.append( "date" )    // adds to end
fruits.prepend( "avocado" ) // adds to front
fruits.isEmpty()           // false
fruits.contains( "banana" ) // true
fruits.find( "cherry" )    // 3 (1-based index, 0 if not found)
fruits.each( (f) -> println(f) )
fruits.map( (f) -> uCase(f) )
fruits.filter( (f) -> f.startsWith("a") )
fruits.reduce( (acc, f) -> acc & "," & f, "" )

// Inline array literal
var nums = [ 1, 2, 3, 4, 5 ]

// Spread (v1.12+)
var more = [ ...nums, 6, 7 ]
```

### Varargs / Positional Java Calls Must Use Arrays

When calling a Java method that accepts `Object... args` (varargs), you **must**
pass a BoxLang array — a bare single value will not work:

```boxlang
// WRONG — bare value
storage.query( "SELECT * FROM users WHERE id = ?", userId )

// CORRECT — wrapped in an array
storage.query( "SELECT * FROM users WHERE id = ?", [userId] )

// Multiple params
storage.query( "SELECT * FROM users WHERE role = ? AND active = ?", [role, active] )
```

---

## Semicolons

Semicolons are **optional** at the end of statements and are considered noisy.
Do **not** add them to variable declarations, function calls, return statements,
or control-flow blocks.

```boxlang
// BAD — noisy semicolons
var name = "BoxLang";
var total = items.len();
return total;

// GOOD — clean, no semicolons
var name = "BoxLang"
var total = items.len()
return total
```

Semicolons ARE required (or conventional) in two places:

1. **Self-closing component tags** — terminate the tag invocation:
   ```boxlang
   bx:header name="Content-Type" value="application/json";
   bx:location url="/login" addToken=false;
   bx:abort;
   ```

2. **Property declarations** inside a class:
   ```boxlang
   class MyComponent {
       bx:property name="title" type="string" default="";
       bx:property name="count" type="numeric" default=0;
   }
   ```

---

## Type System

BoxLang is dynamically typed with optional type enforcement:

```boxlang
// Type annotations (optional)
string function greet( required string name ) {
    return "Hello, #name#!"
}

// Auto-casting
var num = "42" + 0      // 42 (string auto-cast to number)
var bool = "true"       // truthy
var date = "2024-01-15" // auto-cast to date in date functions

// Type checking
isNumeric( val )
isDate( val )
isArray( val )
isStruct( val )
isNull( val )
getMetaData( obj ).name   // introspect type
```

## Modern Features (v1.12+)

```boxlang
// Array destructuring
var [first, second, ...rest] = myArray
var [a, b] = [1, 2]

// Struct destructuring
var { name, age } = person
var { name: fullName, age: years } = person  // rename

// Spread in function calls
var args = [1, 2, 3]
sum( ...args )

// Spread in array/struct literals
var combined = [...arr1, ...arr2]
var merged = {...struct1, ...struct2}

// For-loop destructuring
for ( var [key, val] in myStruct ) { ... }
for ( var [index, item] in myArray ) { ... }
```

## Comments

```boxlang
// Single-line comment

/* Multi-line
   comment */

/**
 * Doc-comment (used for annotation metadata)
 * @param name The user's name
 * @return Greeting string
 */
```

## Built-in Functions (BIFs)

BIFs are globally available without imports. Call them directly or as member functions:

```boxlang
// Function-style
len( myArray )
arrayAppend( myArray, item )
structKeyExists( myStruct, "key" )

// Member-function style (preferred)
myArray.len()
myArray.append( item )
myStruct.keyExists( "key" )

// List all available BIFs
writeDump( getFunctionList() )
```

## Output

```boxlang
writeOutput( "Hello" )      // write to output buffer
println( "Hello" )          // write + newline (scripts)
dump( var=myVar )           // debug dump
writeDump( myVar )          // alias for dump
abort                       // stop execution
```

## Modern Function Syntax

### Method Declaration Without `function` Keyword

Inside a `class` body, the `function` keyword is optional. BoxLang supports a
concise declaration style with optional colon-based return type annotations:

```boxlang
class MathService {

    // Concise declaration — no "function" keyword
    add( numeric a, numeric b ) {
        return a + b
    }

    // With return-type annotation (colon syntax)
    multiply( required numeric a, required numeric b ):numeric {
        return a * b
    }

    // With default parameter value and return type
    power( required numeric base, numeric exponent = 2 ):numeric {
        return base ^ exponent
    }

    // Void-like — no return type declared
    logOperation( required string operation ) {
        writeLog( "Operation: #operation#" )
    }
}
```

Available return types: `string`, `numeric`, `boolean`, `array`, `struct`, `query`,
`date`, `void`, `any`, or a fully-qualified class name.

### Traditional `function` Keyword (Still Valid)

```boxlang
class Service {
    function calculate( required numeric value ):numeric {
        return value * 2
    }
}
```

Both styles are equivalent. Prefer the concise style inside classes; use the
`function` keyword for standalone script-level functions and closures.

## Quick-Reference: Common Pitfalls

| Pitfall | Wrong | Correct |
|---------|-------|--------|
| Array indexing | `arr[ 0 ]` | `arr[ 1 ]` or `arr.first()` |
| Trailing semicolons | `var x = 1;` | `var x = 1` |
| Varargs calls | `query( sql, singleVal )` | `query( sql, [singleVal] )` |
| CFML functions in BoxLang | `cfheader( ... )` | `bx:header name=... value=...;` |
| Java-style `//` not needed | n/a | Both `//` and `/* */` work |

## References

- [BoxLang Syntax Docs](https://boxlang.ortusbooks.com/boxlang-language/syntax)
- [Variables & Scopes](https://boxlang.ortusbooks.com/boxlang-language/variables)
- [Operators](https://boxlang.ortusbooks.com/boxlang-language/operators)
- [Control Flow](https://boxlang.ortusbooks.com/boxlang-language/control-flow)
- [Exception Handling](https://boxlang.ortusbooks.com/boxlang-language/exception-handling)