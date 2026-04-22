---
title: CFML Core Guidelines
description: CFML syntax styles, components, functions, scopes, queries, error handling, and best practices
---

# CFML Core Guidelines

## Overview

CFML (ColdFusion Markup Language) is a dynamic, rapid application development language for the JVM. It supports both tag-based and script-based syntax, making it flexible for different coding styles.

## Syntax Styles

CFML supports two syntax styles that can be mixed in the same file:

### Script Syntax (Recommended)

```cfml
component {
    property name="userService" inject;

    function getAll() {
        return userService.findAll();
    }

    function create( required struct data ) {
        return userService.create( data );
    }
}
```

### Tag Syntax

```cfml
<cfcomponent>
    <cffunction name="getAll" access="public" returntype="array">
        <cfreturn userService.findAll()>
    </cffunction>
</cfcomponent>
```

**Best Practice:** Use script syntax (CFScript) for better readability and consistency with modern languages.

## Component Structure

### Basic Component

```cfml
component {
    // Properties
    property name="firstName";
    property name="lastName";
    property name="email";

    // Constructor
    function init( required string firstName, required string lastName ) {
        variables.firstName = arguments.firstName;
        variables.lastName = arguments.lastName;
        return this;
    }

    // Methods
    function getFullName() {
        return variables.firstName & " " & variables.lastName;
    }

    function setEmail( required string email ) {
        variables.email = arguments.email;
    }
}
```

### Accessors

```cfml
component accessors="true" {
    property name="firstName" type="string";
    property name="lastName" type="string";
    property name="age" type="numeric";
}

// Automatically generates:
// - getFirstName()
// - setFirstName( string firstName )
// - getLastName()
// - setLastName( string lastName )
// - getAge()
// - setAge( numeric age )
```

## Functions

### Function Declaration

```cfml
// Public function
function getUserById( required numeric id ) {
    return userDAO.find( arguments.id );
}

// Private function
private function validateUser( required struct user ) {
    // Validation logic
    return true;
}

// Typed function
public array function getActiveUsers() {
    return userDAO.findAll().filter( function( user ) {
        return user.active;
    } );
}

// Function with default arguments
function sendEmail(
    required string to,
    required string subject,
    string from = "noreply@example.com",
    boolean html = true
) {
    // Email logic
}
```

## Data Types

### Arrays

```cfml
// Array creation
var items = [];
var numbers = [ 1, 2, 3, 4, 5 ];
var users = [ { name: "Luis" }, { name: "Brad" } ];

// Array methods
items.append( "new item" );
items.prepend( "first item" );
var length = items.len();
var hasItem = items.find( "value" );

// Iteration
items.each( function( item ) {
    writeOutput( item );
} );

// Map/Filter/Reduce
var doubled = numbers.map( function( n ) { return n * 2; } );
var evens = numbers.filter( function( n ) { return n % 2 == 0; } );
var sum = numbers.reduce( function( acc, n ) { return acc + n; }, 0 );
```

### Structs

```cfml
// Struct creation
var user = {};
var person = {
    firstName: "Luis",
    lastName: "Majano",
    age: 40
};

// Accessing values
var name = user.firstName;
var name = user[ "firstName" ];

// Struct methods
user.append( { email: "luis@email.com" } );
var keys = user.keyArray();
var values = user.valueArray();
var hasKey = user.keyExists( "email" );

// Iteration
user.each( function( key, value ) {
    writeOutput( "#key#: #value#" );
} );
```

### Queries

```cfml
// QueryExecute (modern, recommended)
var qUsers = queryExecute(
    "SELECT * FROM users WHERE active = :active",
    { active: true },
    { datasource: "myDB" }
);

// Query properties
var rowCount = qUsers.recordCount;
var columnList = qUsers.columnList;

// Query iteration
for ( var row in qUsers ) {
    writeOutput( row.firstName & " " & row.lastName );
}

qUsers.each( function( row, index ) {
    writeOutput( row.email );
} );

// Query of queries
var filtered = queryExecute(
    "SELECT * FROM qUsers WHERE age > :minAge",
    { minAge: 18 },
    { dbtype: "query" }
);
```

## Control Flow

### Conditionals

```cfml
// If/else if/else
if ( user.active ) {
    sendWelcomeEmail( user );
} else if ( user.pending ) {
    sendReminderEmail( user );
} else {
    logInactiveUser( user );
}

// Ternary operator
var status = user.active ? "Active" : "Inactive";

// Elvis operator (null coalescing)
var displayName = user.nickname ?: user.firstName;

// Switch
switch ( status ) {
    case "pending":
        processPending();
        break;
    case "approved":
        processApproved();
        break;
    case "rejected":
        processRejected();
        break;
    default:
        handleUnknown();
}
```

### Loops

```cfml
// For loop
for ( var i = 1; i <= 10; i++ ) {
    writeOutput( i );
}

// For-in loop (arrays)
for ( var item in items ) {
    writeOutput( item );
}

// For-in loop (structs)
for ( var key in user ) {
    writeOutput( "#key#: #user[ key ]#" );
}

// While loop
var i = 1;
while ( i <= 10 ) {
    writeOutput( i );
    i++;
}

// Array each
items.each( function( item, index ) {
    writeOutput( "#index#: #item#" );
} );
```

## Exception Handling

```cfml
try {
    var user = userService.getById( id );
    processUser( user );
} catch ( EntityNotFound e ) {
    writeLog( type="error", text="User not found: #id#" );
    writeDump( e );
    // Handle specific exception
} catch ( database e ) {
    writeLog( type="fatal", text="Database error: #e.message#" );
    // Handle database errors
} catch ( any e ) {
    writeLog( type="error", text="Unexpected error: #e.message#" );
    rethrow;
} finally {
    // Cleanup code (always executes)
    cleanup();
}

// Throw custom exception
if ( !isValid( "email", email ) ) {
    throw(
        type = "ValidationException",
        message = "Invalid email address",
        detail = "Email: #email#"
    );
}
```

## Built-in Functions (BIFs)

### String Functions

```cfml
var str = "Hello World";
var upper = str.ucase();                    // HELLO WORLD
var lower = str.lcase();                    // hello world
var length = str.len();                     // 11
var contains = str.find( "World" );         // 7
var replaced = str.replace( "World", "CFML" ); // Hello CFML
var trimmed = "  text  ".trim();           // text
var split = str.listToArray( " " );        // [ "Hello", "World" ]
```

### Array Functions

```cfml
var arr = [ 1, 2, 3, 4, 5 ];
arr.append( 6 );                           // [ 1, 2, 3, 4, 5, 6 ]
arr.prepend( 0 );                          // [ 0, 1, 2, 3, 4, 5, 6 ]
var length = arr.len();                    // 7
var slice = arr.slice( 2, 4 );            // [ 1, 2, 3 ]
var sorted = arr.sort( "numeric" );        // [ 0, 1, 2, 3, 4, 5, 6 ]
var unique = [ 1, 2, 2, 3 ].arrayUnique(); // [ 1, 2, 3 ]
```

### Struct Functions

```cfml
var user = { name: "Luis", age: 40 };
user.keyExists( "name" );                  // true
var keys = user.keyArray();                // [ "name", "age" ]
var values = user.valueArray();            // [ "Luis", 40 ]
var isEmpty = user.isEmpty();              // false
user.delete( "age" );                      // Removes age key
```

### Date Functions

```cfml
var now = now();                           // Current date/time
var today = dateFormat( now, "yyyy-mm-dd" );
var time = timeFormat( now, "HH:mm:ss" );
var tomorrow = dateAdd( "d", 1, now );
var diff = dateDiff( "d", startDate, endDate );
var parsed = parseDateTime( "2024-01-01" );
```

## Best Practices

- **Use CFScript** over tag-based syntax for consistency
- **Leverage accessors** for automatic getters/setters
- **Use queryExecute()** instead of `<cfquery>` tags
- **Scope all variables** explicitly (var, variables, arguments)
- **Use member functions** on arrays, structs, and strings (`.map()`, `.filter()`, etc.)
- **Handle exceptions** appropriately with specific catch blocks
- **Use transactions** for database operations that need atomicity
- **Cache expensive operations** using CacheBox
- **Log important events** using LogBox
- **Validate input** before processing

## Documentation

For complete CFML documentation and built-in functions, visit:
- https://cfdocs.org
- https://modern-cfml.ortusbooks.com

---

> **Implementation patterns are in skills.** Load the relevant skill with `read_file` when implementing:
> - ColdBox handler patterns: `.ai/skills/coldbox-handler-development/SKILL.md`
> - REST APIs: `.ai/skills/coldbox-rest-api-development/SKILL.md`
> - Testing: `.ai/skills/coldbox-testing-handler/SKILL.md`
