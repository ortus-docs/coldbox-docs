---
name: boxlang-functional-programming
description: "Use this skill when working with BoxLang lambdas, closures, arrow functions, higher-order functions, functional array/struct pipelines (map, filter, reduce, flatMap, groupBy, etc.), destructuring, or spread syntax."
---

# BoxLang Functional Programming

## Overview

BoxLang treats functions as first-class values. Functions can be stored in variables,
passed as arguments, returned from other functions, and used in functional pipelines.
BoxLang supports closures, lambdas, and arrow functions with a clean, expressive syntax.

## Closures vs Lambdas — Critical Distinction

**This distinction matters for correctness:**

| Syntax | Name | Scope Capture | Use When |
|--------|------|---------------|----------|
| `(args) => expr` | Closure | ✅ Yes — captures surrounding scope | Accesses outer variables or calls external functions/BIFs |
| `(args) -> expr` | Lambda | ❌ No — only uses its own args | Purely deterministic; only works with passed arguments |
| `function(...) {}` | UDF/Closure | ✅ Yes — when assigned to a variable/returned | Named or complex multi-line function bodies |

```boxlang
var multiplier = 10

// ✅ Closure (=>) — captures 'multiplier' from outer scope
var scale = ( n ) => n * multiplier
scale( 5 )   // 50

// ✅ Lambda (->) — does NOT capture, only uses its own arguments
var double = ( n ) -> n * 2
double( 5 )  // 10

// ❌ WRONG — lambda trying to access outer 'multiplier' will fail
var scale = ( n ) -> n * multiplier  // Error! multiplier not in lambda scope
```

**Rule of thumb:** If the function body references any variable from the surrounding scope
or calls external functions/BIFs (like `now()`, `writeLog()`, etc.), use `=>` (closure).
If it only processes its own arguments, use `->` (lambda).

## Closures

A closure captures its surrounding variable scope using `=>` or the `function` keyword:

```boxlang
// Closure with fat-arrow syntax (captures outer scope)
var greet = ( required string name ) => "Hello, #name#!"
writeOutput( greet( "BoxLang" ) )  // Hello, BoxLang!

// Closure with function keyword
var greet = function( required string name ) {
    return "Hello, #name#!"
}

// Closure capturing outer variables (must use => or function)
function makeCounter() {
    var count = 0
    return () => {    // => required because it captures 'count'
        count++
        return count
    }
}

var counter = makeCounter()
counter()   // 1
counter()   // 2
counter()   // 3
```

## Lambdas

Lambdas (`->`) are deterministic — they do NOT capture surrounding scope. Use them
only when the function body is self-contained with its own arguments:

```boxlang
// Lambda: (args) -> expression (no return keyword)
var double   = (n) -> n * 2
var square   = (n) -> n ^ 2
var add      = (a, b) -> a + b

double( 5 )   // 10
add( 3, 4 )   // 7

// Block-body lambda (with return)
var process = (item) -> {
    var result = item.trim()
    return result.uCase()   // only uses item, no outer scope
}
```

## Arrow Functions (`=>`)

Arrow functions use `=>` and create closures that capture the surrounding scope:

```boxlang
// Expression body (implicit return)
var toUpper = (s) => s.uCase()

// Block body with explicit return
var validate = (email) => {
    return email.contains( "@" ) && email.contains( "." )
}

// Single argument — parentheses optional
var increment = n => n + 1
```

## Higher-Order Functions

```boxlang
// Passing a lambda as argument (purely processes its own args)
function applyTwice( required function fn, required any value ) {
    return fn( fn( value ) )
}

applyTwice( (n) -> n * 2, 3 )   // 12

// Returning a closure (captures 'factor' from outer scope — use =>)
function multiplierOf( required numeric factor ) {
    return (n) => n * factor    // => because it captures 'factor'
}

var triple = multiplierOf( 3 )
triple( 7 )   // 21
```

## Array Functional Methods

All collection methods accept a closure or lambda as the callback.

### `map` — transform each element

```boxlang
var numbers = [1, 2, 3, 4, 5]
var doubled = numbers.map( (n) -> n * 2 )   // lambda: only uses 'n'
// [2, 4, 6, 8, 10]

var users = getUsers()
var names = users.map( (u) -> u.getName() )
```

### `filter` — keep matching elements

```boxlang
var evens = numbers.filter( (n) -> n % 2 == 0 )   // lambda
// [2, 4]

// Closure when filtering against an outer variable
var threshold = 3
var big = numbers.filter( (n) => n > threshold )   // closure: captures 'threshold'

var activeUsers = users.filter( (u) -> u.isActive() )
```

### `reduce` — fold to a single value

```boxlang
var sum = numbers.reduce( (acc, n) -> acc + n, 0 )   // lambda
// 15

var index = users.reduce( function( acc, user ) {
    acc[ user.getId() ] = user
    return acc
}, {} )
```

### `each` — iterate with side effects

```boxlang
numbers.each( (n) -> writeOutput( n & " " ) )

users.each( function( user, index ) {
    logService.log( "#index#: #user.getEmail()#" )
})
```

### `flatMap` — map then flatten one level

```boxlang
var sentences = ["hello world", "foo bar"]
var words = sentences.flatMap( (s) -> s.listToArray( " " ) )
// ["hello", "world", "foo", "bar"]
```

### `groupBy` — group elements by a key

```boxlang
var byStatus = users.groupBy( (u) -> u.getStatus() )
// { "active": [...], "inactive": [...] }
```

### `chunk` — split into sub-arrays

```boxlang
var batches = [1,2,3,4,5,6,7].chunk( 3 )
// [[1,2,3],[4,5,6],[7]]
```

### `unique` — remove duplicates

```boxlang
var deduped = [1, 2, 2, 3, 3, 3].unique()
// [1, 2, 3]
```

### `reject` — opposite of filter

```boxlang
var inactive = users.reject( (u) -> u.isActive() )
```

### `zip` — pair elements from two arrays

```boxlang
var keys = ["a", "b", "c"]
var vals = [1, 2, 3]
var pairs = keys.zip( vals )
// [["a",1],["b",2],["c",3]]
```

### `transpose` — flip rows and columns

```boxlang
var matrix = [[1,2,3],[4,5,6]]
var transposed = matrix.transpose()
// [[1,4],[2,5],[3,6]]
```

### `sort` — functional sort

```boxlang
var sorted = users.sort( (a, b) -> a.getLastName().compare( b.getLastName() ) )
```

### `find` and `findAll`

```boxlang
var admin = users.find( (u) -> u.getRole() == "admin" )
var admins = users.findAll( (u) -> u.getRole() == "admin" )
```

## Struct Functional Methods

```boxlang
var config = { host: "localhost", port: 8080, debug: true }

// map — transform values
var upper = config.map( (key, val) -> val.toString().uCase() )

// filter — keep matching entries
var strings = config.filter( (key, val) -> isSimpleValue( val ) && isString( val ) )

// reduce
var summary = config.reduce( (acc, key, val) -> acc & "#key#=#val# ", "" )

// each
config.each( (key, val) -> writeOutput( "#key#: #val#<br>" ) )
```

## Function Composition

```boxlang
// Manual composition
function compose( required function f, required function g ) {
    return (x) -> f( g( x ) )
}

var trim      = (s) -> s.trim()
var toLower   = (s) -> s.lCase()
var normalize = compose( toLower, trim )

normalize( "  Hello World  " )   // "hello world"

// Pipeline using array reduce
var pipeline = [
    (s) -> s.trim(),
    (s) -> s.lCase(),
    (s) -> s.replace( " ", "-" )
]

var slug = pipeline.reduce( (val, fn) -> fn( val ), "  Hello World  " )
// "hello-world"
```

## Partial Application

```boxlang
function partial( required function fn, any ...boundArgs ) {
    return function() {
        var allArgs = boundArgs.append( arguments.toArray(), true )
        return fn( argumentCollection=allArgs )
    }
}

var add     = (a, b) -> a + b
var addFive = partial( add, 5 )
addFive( 3 )   // 8
```

## Destructuring with Functional Code (v1.12+)

```boxlang
// Destructure in lambda parameters
var pairs = [["Alice", 30], ["Bob", 25]]
pairs.each( ([name, age]) -> writeOutput( "#name# is #age#" ) )

// Spread in function calls
var args = [1, 2, 3]
var result = sum( ...args )

// Spread in array literals
var merged = [...getDefaults(), ...getOverrides()]
```

## Memoization Pattern

```boxlang
function memoize( required function fn ) {
    var cache = {}
    return function() {
        var key = serializeJSON( arguments )
        if ( !cache.keyExists( key ) ) {
            cache[ key ] = fn( argumentCollection=arguments )
        }
        return cache[ key ]
    }
}

var expensiveCalc = memoize( (n) -> {
    // ... expensive computation
    return n * n
})
```

## Java Streams Integration

BoxLang collections expose a `.stream()` method that returns a lazy Java Stream,
enabling functional-style pipelines with deferred evaluation.

### Creating Streams

```boxlang
// From array (lazy — nothing computed yet)
var stream = [ 1, 2, 3, 4, 5 ].stream()

// From query
var users = queryExecute( "SELECT * FROM users" )
var userStream = users.stream()

// From struct
var structStream = { name: "Ada", age: 36 }.stream()

// From numeric range
var rangeStream = range( 1, 100 ).stream()

// Infinite stream (always use .limit() before collecting)
import java.util.stream.Stream
var infinite = Stream.iterate( 0, ( n ) => n + 1 )
```

### Intermediate Operations (Lazy)

These operations build the pipeline but do NOT execute until a terminal operation is called:

```boxlang
var result = [ 1, 2, 3, 4, 5, 6 ]
    .stream()
    .filter( ( n ) => n % 2 == 0 )        // keep even numbers
    .map( ( n ) => n * 2 )                 // double each
    .distinct()                            // remove duplicates
    .sorted()                              // sort ascending
    .sorted( ( a, b ) => b - a )           // sort descending
    .limit( 3 )                            // take first 3
    .skip( 1 )                             // skip first 1
    .peek( ( n ) => log.debug( n ) )       // side-effect without modifying
    .flatMap( ( arr ) => arr.stream() )    // flatten nested arrays
    .collect()
```

### Terminal Operations (Execute the Pipeline)

```boxlang
var numbers = [ 1, 2, 3, 4, 5 ]

// collect() — materialize to array
var evens = numbers.stream().filter( ( n ) => n % 2 == 0 ).collect()
// Result: [ 2, 4 ]

// count()
var count = numbers.stream().filter( ( n ) => n > 3 ).count()  // 2

// reduce() — fold into a single value
var sum = numbers.stream().reduce( 0, ( acc, n ) => acc + n )  // 15

// forEach() — side effects
numbers.stream().forEach( ( n ) => writeOutput( n ) )

// anyMatch / allMatch / noneMatch
var hasEven    = numbers.stream().anyMatch( ( n ) => n % 2 == 0 )   // true
var allPositive = numbers.stream().allMatch( ( n ) => n > 0 )        // true
var noNeg      = numbers.stream().noneMatch( ( n ) => n < 0 )       // true

// findFirst()
var first = numbers.stream().filter( ( n ) => n > 3 ).findFirst()
// Returns an Optional — use .get() or .orElse( default )
var value = first.isPresent() ? first.get() : 0
```

### Practical Stream Pipeline

```boxlang
// Extract active user names, sorted, uppercased
var activeNames = queryExecute( "SELECT * FROM users" )
    .stream()
    .filter( ( user ) => user.active )
    .map( ( user ) => "#user.firstName# #user.lastName#" )
    .map( ( name ) => name.uCase() )
    .sorted()
    .collect()

// Summary struct via reduce
var summary = orders
    .stream()
    .reduce(
        { total: 0, count: 0 },
        ( acc, order ) => ({ total: acc.total + order.amount, count: acc.count + 1 })
    )
```

### Parallel Streams

```boxlang
// parallelStream() — processes elements concurrently
var results = largeDataset
    .parallelStream()
    .filter( ( item ) => item.active )
    .map( ( item ) => expensiveTransform( item ) )
    .collect()
// ⚠️ Only use for CPU-bound, stateless operations on large datasets
```

## References

- [Closures & Lambdas](https://boxlang.ortusbooks.com/boxlang-language/closures-lambdas)
- [Arrays](https://boxlang.ortusbooks.com/boxlang-language/arrays)
- [Structures](https://boxlang.ortusbooks.com/boxlang-language/structures)
- [Functions](https://boxlang.ortusbooks.com/boxlang-language/functions)