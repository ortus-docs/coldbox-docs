---
name: boxlang-classes-and-oop
description: "Use this skill when writing BoxLang classes, components, interfaces, inheritance hierarchies, annotations, properties, constructors, or applying object-oriented design patterns in BoxLang."
---

# BoxLang Classes and Object-Oriented Programming

## Overview

BoxLang classes are defined in `.bx` files. A class is a blueprint that encapsulates
properties (data) and functions/methods (behavior). BoxLang supports single
inheritance, multiple interface implementation, abstract classes, and static members.

## Basic Class Structure

```boxlang
// File: models/User.bx
class accessors="true" {

    // Properties (auto-generates getters/setters when accessors="true")
    property name="firstName" type="string"
    property name="lastName"  type="string"
    property name="email"     type="string"
    property name="createdAt" type="date"

    // Constructor
    function init( required string firstName, required string lastName, required string email ) {
        variables.firstName = arguments.firstName
        variables.lastName  = arguments.lastName
        variables.email     = arguments.email
        variables.createdAt = now()
        return this
    }

    // Method
    string function getFullName() {
        return "#variables.firstName# #variables.lastName#"
    }

}
```

## Scopes Inside a Class

| Scope | Access | Use |
|-------|--------|-----|
| `variables` | Private | Internal state, private methods |
| `this` | Public | Public methods exposed to callers |
| `static` | Class-level | Shared across all instances |

## Instantiation

```boxlang
// Using `new`
var user = new models.User( "Ada", "Lovelace", "ada@example.com" )

// Using createObject
var user = createObject( "class", "models.User" ).init( "Ada", "Lovelace", "ada@example.com" )

// Call methods
writeOutput( user.getFullName() )

// With accessors="true", getters/setters are auto-generated:
user.setFirstName( "Grace" )
writeOutput( user.getFirstName() )
```

## Class Attributes

```boxlang
class
    extends="models.BaseEntity"
    implements="contracts.ISerializable,contracts.IValidatable"
    accessors="true"
    serializable="true"
{
    // ...
}
```

| Attribute | Description |
|-----------|-------------|
| `extends` | Parent class (single inheritance) |
| `implements` | Comma-separated interfaces |
| `accessors` | Auto-generate getters/setters for properties |
| `serializable` | Enable Java serialization |
| `persistent` | ORM persistence (with bx-orm module) |

## Inheritance

```boxlang
// Base class: models/Animal.bx
class {
    property name="name" type="string"

    function init( required string name ) {
        variables.name = arguments.name
        return this
    }

    string function speak() {
        return "..."
    }

    string function getName() {
        return variables.name
    }
}

// Subclass: models/Dog.bx
class extends="models.Animal" {

    function init( required string name ) {
        // Call parent constructor
        super.init( arguments.name )
        return this
    }

    // Override parent method
    string function speak() {
        return "Woof! I am #getName()#"
    }

    // New method
    function fetch( required string item ) {
        return "Fetching #arguments.item#!"
    }

}
```

## Interfaces

```boxlang
// contracts/IRepository.bx
interface {
    // Declare required method signatures
    any function findById( required numeric id )
    array function findAll()
    any function save( required any entity )
    void function delete( required numeric id )
}

// Implementing an interface
class implements="contracts.IRepository" {
    any function findById( required numeric id ) {
        return queryExecute( "SELECT * FROM users WHERE id = :id", { id: arguments.id } )
    }
    // ... implement all interface methods
}
```

## Abstract Classes

```boxlang
abstract class {

    // Concrete method (inherited as-is)
    function log( required string message ) {
        writeOutput( "[LOG] #arguments.message#" )
    }

    // Abstract method (subclass MUST implement)
    abstract string function getType()
    abstract function process( required any data )

}
```

## Static Members

```boxlang
class {

    // Static property (shared across all instances)
    static {
        variables.instanceCount = 0
        variables.DEFAULT_TIMEOUT = 30
    }

    function init() {
        static.instanceCount++
        return this
    }

    // Static method — call without an instance
    static numeric function getInstanceCount() {
        return static.instanceCount
    }

}

// Usage
var count = MyClass.getInstanceCount()
```

## Properties

```boxlang
class accessors="true" {

    // Basic property
    property name="title" type="string"

    // With default value
    property name="status" type="string" default="active"

    // Read-only (no setter)
    property name="id" type="numeric" setter="false"

    // Custom getter/setter names
    property name="isActive" type="boolean" getter="isActive" setter="setActive"

    // Private — no getter/setter generated
    property name="secretKey" type="string" getter="false" setter="false"

}
```

## Pseudo-Constructor

Code outside any function but inside the class body runs before `init()`:

```boxlang
class accessors="true" {

    property name="items" type="array"

    // Pseudo-constructor: runs first when class is loaded
    variables.items = []
    variables.createdAt = now()

    function init() {
        // By now pseudo-constructor has already run
        return this
    }
}
```

## Annotations

BoxLang supports two annotation syntaxes: standalone `@` style and inline/Javadoc style.

```boxlang
// Standalone @ syntax (preferred for framework metadata)
@singleton
@cacheName( "users" )
class UserService {

    @inject( "UserRepository" )
    property userRepository

    @cached
    @timeout( 300 )
    array function getAll() {
        return queryExecute( "SELECT * FROM users" ).toArray()
    }

}

// Inline annotation style (class attribute)
class singleton {
    // ...
}

// Javadoc-style (compatible, used for documentation generators)
/**
 * User service.
 * @singleton
 * @author Ada Lovelace
 */
class {
    // ...
}
```

## Final Constructs

`final` prevents extension, overriding, or reassignment:

```boxlang
// Final class — cannot be extended
final class CryptoUtil {
    // ...
}

// Final method — cannot be overridden by subclasses
class BaseService {
    final function getConfig() {
        return variables.config
    }
}

// Final variable — constant (cannot be reassigned after construction)
class {
    final static CACHE_PREFIX = "app_"
    final MAX_RETRIES = 3

    // variables.MAX_RETRIES = 5  → throws exception
    // References inside final objects CAN be mutated:
    final config = { debug: false }
    config[ "debug" ] = true  // ✅ allowed (mutating contents)
    config = {}               // ❌ throws final variable exception
}
```

## Method Chaining (Fluent Interface)

```boxlang
class {

    property name="query" type="string"
    property name="params" type="struct"
    property name="limitVal" type="numeric"

    function where( required string condition ) {
        variables.query &= " WHERE #arguments.condition#"
        return this  // enables chaining
    }

    function limit( required numeric n ) {
        variables.limitVal = arguments.n
        return this
    }

    array function execute() {
        return queryExecute( variables.query, variables.params ).toArray()
    }

}

// Usage
var results = new QueryBuilder()
    .where( "status = 'active'" )
    .limit( 10 )
    .execute()
```

## Access Modifiers

```boxlang
class {

    // public (default) — accessible from outside
    public string function publicMethod() { ... }

    // private — only accessible within this class
    private string function privateHelper() { ... }

    // package — accessible within same package/directory
    package string function packageMethod() { ... }

    // remote — accessible via HTTP/web service
    remote string function apiEndpoint() returnformat="json" { ... }

}
```

## References

- [BoxLang Classes](https://boxlang.ortusbooks.com/boxlang-language/classes)
- [OOP Concepts](https://boxlang.ortusbooks.com/boxlang-language/object-oriented-programming)
- [Interfaces](https://boxlang.ortusbooks.com/boxlang-language/interfaces)
- [Annotations](https://boxlang.ortusbooks.com/boxlang-language/annotations)