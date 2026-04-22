---
name: boxlang-testing
description: "Use this skill when writing, running, or debugging tests for BoxLang applications using TestBox: BDD-style describe/it specs, xUnit-style test classes, expectations (expect/toBe matchers), assertions ($assert), life-cycle methods (beforeAll/afterAll/beforeEach/afterEach/aroundEach), MockBox mocking (createMock/prepareMock/$()/$results()), mock data generation (mockData()), async testing, exception testing, focused/skipped specs, and running tests via the BoxLang CLI runner."
---

# BoxLang Testing with TestBox

## Overview

TestBox is the standard testing framework for BoxLang. It supports two styles:

- **BDD** (Behavior-Driven Development) — `describe()`, `it()`, `feature()`, `story()`, `given()/when()/then()`
- **xUnit** — class-based, `test*()` methods, `setup()`/`tearDown()`

Both styles use the same assertions/expectations library and MockBox for mocking.

---

## Test Bundles

A test bundle is a BoxLang class (`.bx` file) that contains your tests. Name files
with `Test` or `Spec` suffix by convention: `UserServiceSpec.bx`, `OrderServiceTest.bx`.

```boxlang
// tests/specs/UserServiceSpec.bx
class extends="testbox.system.BaseSpec" {

    function run() {
        describe( "UserService", () => {
            it( "can greet a user", () => {
                expect( "hello" ).toBe( "hello" )
            } )
        } )
    }

}
```

Extending `testbox.system.BaseSpec` is optional but recommended: it enables IDE
introspection, direct web runner execution, and faster test loading.

### Injected Variables (always available in bundles)

| Variable | Purpose |
|---|---|
| `$mockbox` | MockBox instance for creating mocks |
| `$assert` | Assertions library |
| `$utility` | Utility helpers |
| `$customMatchers` | Custom matcher registry |
| `$testID` | Unique ID for the bundle |
| `$debugBuffer` | Debug output buffer |

---

## BDD Style

### Basic Structure

```boxlang
class extends="testbox.system.BaseSpec" {

    function run() {

        describe( "Calculator", () => {

            it( "can add two numbers", () => {
                var calc = new Calculator()
                expect( calc.add( 1, 2 ) ).toBe( 3 )
            } )

            it( "throws on divide by zero", () => {
                expect( () => new Calculator().divide( 10, 0 ) ).toThrow()
            } )

        } )

    }

}
```

### Suite Functions (all equivalent blocks)

| Function | Alias / Purpose |
|---|---|
| `describe( title, body )` | Standard suite |
| `feature( title, body )` | Feature-level suite |
| `story( title, body )` | Story-level suite |
| `scenario( title, body )` | Scenario in feature/story |
| `given( title, body )` | Given block (GWT) |
| `when( title, body )` | When block (GWT) |
| `then( title, body )` | Then block / alias for `it()` |

```boxlang
feature( "User authentication", () => {
    scenario( "valid credentials", () => {
        given( "a registered user", () => {
            when( "they log in with correct password", () => {
                then( "they should receive a JWT token", () => {
                    var token = authService.login( "user@example.com", "secret" )
                    expect( token ).notToBeEmpty()
                } )
            } )
        } )
    } )
} )
```

### Spec Functions

```boxlang
// it() is the primary spec block
it( "does something", () => {
    expect( true ).toBeTrue()
} )

// then() is an alias for it() — title arg is "then" instead of "title"
then( "something should happen", () => {
    expect( result ).notToBeNull()
} )
```

---

## Life-Cycle Methods (BDD)

Life-cycle methods control setup and teardown around specs.

### Global (whole bundle)

```boxlang
class extends="testbox.system.BaseSpec" {

    // Runs ONCE before all specs in the bundle
    function beforeAll() {
        variables.db = new Database()
        variables.db.connect()
    }

    // Runs ONCE after all specs in the bundle
    function afterAll() {
        variables.db.disconnect()
        directoryDelete( "/tests/tmp", true )
    }

    function run() { ... }

}
```

### Suite-Level (BDD: `beforeEach` / `afterEach` / `aroundEach`)

```boxlang
describe( "UserService", () => {

    beforeEach( ( currentSpec, data ) => {
        variables.service = new UserService()
    } )

    afterEach( ( currentSpec, data ) => {
        variables.service = javaCast( "null", "" )
    } )

    aroundEach( ( spec, suite, data ) => {
        // wraps the spec; you MUST call spec.body() yourself
        transaction action="begin" {
            arguments.spec.body()
            transaction action="rollback"
        }
    } )

    it( "can find a user by id", () => {
        var user = variables.service.findById( 1 )
        expect( user ).toHaveKey( "name" )
    } )

} )
```

> `beforeEach` walks **down** the nesting tree (outer → inner).
> `afterEach` walks **up** the tree (inner → outer).

### Life-Cycle Annotations (for parent classes)

Annotate any function to hook into the life-cycle without overriding named methods.
Useful for base test classes:

```boxlang
class extends="testbox.system.BaseSpec" {

    /**
     * @beforeAll
     */
    function initAppContext() {
        // Called once before all specs; discovered up the inheritance chain
    }

    /**
     * @aroundEach
     */
    function wrapInTransaction( spec, suite ) {
        transaction action="begin" {
            try {
                arguments.spec.body()
            } finally {
                transaction action="rollback"
            }
        }
    }

}
```

### Life-Cycle Data Binding

Pass data to life-cycle methods so dynamic values are captured correctly in loops:

```boxlang
var fixtures = [ "admin", "guest", "editor" ]

for ( var role in fixtures ) {
    describe( "Role: #role#", () => {

        beforeEach(
            data = { roleName = role },
            body = ( currentSpec, data ) => {
                variables.user = userFactory.create( data.roleName )
            }
        )

        it(
            title = "has correct permissions",
            data  = { role = role },
            body  = ( data ) => {
                expect( variables.user.getRole() ).toBe( data.role )
            }
        )

    } )
}
```

---

## xUnit Style

```boxlang
@DisplayName "OrderService Tests"
class extends="testbox.system.BaseSpec" {

    // Runs ONCE before all tests
    function beforeTests() {
        application.dbPool = createDBPool()
    }

    // Runs ONCE after all tests
    function afterTests() {
        structClear( application )
    }

    // Runs before EACH test method
    function setup() {
        variables.service = new OrderService()
    }

    // Runs after EACH test method
    function tearDown() {
        variables.service = javaCast( "null", "" )
    }

    // Any function with "test" prefix is a test
    function testCalculateTotalWithDiscount() {
        var total = variables.service.calculateTotal( items=[], discount=0.1 )
        $assert.isEqual( total, 0 )
    }

    // Or use @test annotation for any naming
    @DisplayName "It should reject empty orders"
    @test
    function itRejectsEmptyOrders() {
        expect( () => variables.service.place( [] ) ).toThrow( type="EmptyOrderException" )
    }

    // Skip a specific test
    function testSkipped() skip {
        $assert.fail( "should not run" )
    }

}
```

---

## Expectations (Fluent API)

Use `expect( actual )` to start a fluent assertion chain. Prefix any matcher
with `not` for the negative version.

### Common Matchers

```boxlang
// Equality
expect( result ).toBe( expected )
expect( result ).notToBe( expected )
expect( "Hello" ).toBeWithCase( "Hello" )

// Boolean
expect( isActive ).toBeTrue()
expect( isDeleted ).toBeFalse()

// Null
expect( value ).toBeNull()
expect( value ).notToBeNull()

// Type checks
expect( items ).toBeArray()
expect( config ).toBeStruct()
expect( id ).toBeNumeric()
expect( dob ).toBeDate()
expect( obj ).toBeComponent()
expect( obj ).toBeInstanceOf( "com.example.MyClass" )
expect( obj ).toBeTypeOf( "uuid" )         // uses isValid()

// Size / emptiness
expect( [] ).toBeEmpty()
expect( items ).notToBeEmpty()
expect( items ).toHaveLength( 3 )
expect( "Hello" ).toHaveLength( 5 )

// Struct keys
expect( user ).toHaveKey( "name" )
expect( config ).toHaveDeepKey( "database.host" )

// Strings / arrays
expect( message ).toMatch( "^Error" )         // regex, case-insensitive
expect( message ).toMatchWithCase( "ERROR" )  // regex, case-sensitive
expect( items ).toInclude( "admin" )          // case-insensitive
expect( items ).toIncludeWithCase( "Admin" )  // case-sensitive

// Numbers / dates
expect( value ).toBeGT( 0 )
expect( value ).toBeGTE( 1 )
expect( value ).toBeLT( 100 )
expect( value ).toBeLTE( 99 )
expect( value ).toBeBetween( 1, 100 )
expect( score ).toBeCloseTo( 3.14, 0.01 )

// Exceptions (ALWAYS use a closure)
expect( () => service.doThing() ).toThrow()
expect( () => service.doThing() ).toThrow( type="MyException" )
expect( () => service.doThing() ).toThrow( regex="not found" )
expect( () => service.doThing() ).toThrow( type="MyException", regex="not found" )
```

### Chaining

```boxlang
// Multiple matchers on the same value
expect( result )
    .notToBeNull()
    .toBeStruct()
    .toHaveKey( "id" )
    .toHaveKey( "name" )
```

### Collection Expectations (`expectAll`)

Test every element of an array or struct at once:

```boxlang
expectAll( users ).toHaveKey( "name" )
expectAll( scores ).toBeGT( 0 )
expectAll( [ true, true, true ] ).toBeTrue()
expectAll( { a: 2, b: 4, c: 6 } ).toSatisfy( (x) -> x mod 2 == 0 )
```

### Exception Testing Patterns

```boxlang
// Preferred: closure-based
it( "throws on invalid input", () => {
    expect( () => {
        service.process( javaCast( "null", "" ) )
    } ).toThrow( type="InvalidArgumentException" )
} )

// xUnit annotation style
function testThrowsOnNull() expectedException="InvalidArgumentException" {
    service.process( javaCast( "null", "" ) )
}
```

---

## Assertions Library (`$assert`)

The traditional assertions library available via `$assert` in every bundle:

```boxlang
$assert.isTrue( condition, [message] )
$assert.isFalse( condition, [message] )
$assert.isEqual( expected, actual, [message] )
$assert.isNotEqual( expected, actual, [message] )
$assert.isNull( value, [message] )
$assert.isNotNull( value, [message] )
$assert.isEmpty( value, [message] )
$assert.isNotEmpty( value, [message] )
$assert.includes( collection, needle )
$assert.notIncludes( collection, needle )
$assert.includesWithCase( collection, needle )
$assert.throws( closure, [type], [regex], [message] )
$assert.fail( [message] )

// Dynamic assertion methods (BoxLang only)
assertIsTrue( condition )
assertIsEqual( expected, actual )
assertIsNull( value )
// Any $assert method can be called as assert{MethodName}()
```

---

## Skipping and Focusing

### Skip Specs/Suites

Prefix any suite/spec function with `x` OR use the `skip` argument:

```boxlang
// Prefix approach — skip entire suite
xdescribe( "Slow integration tests", () => { ... } )

// Prefix approach — skip single spec
xit( "is temporarily disabled", () => { ... } )

// skip argument — boolean or closure
it( title="runs only on production", skip=!isProduction(), body=() => { ... } )

describe( title="Lucee only", skip=() => !structKeyExists( server, "lucee" ), body=() => {
    it( "uses Lucee ORM", () => { ... } )
} )

// skip() method inside spec — programmatic
it( "can run on multiple engines", () => {
    if ( !server.keyExists( "boxlang" ) ) {
        skip( "BoxLang-only feature" )
    }
    // ... rest of spec
} )
```

### Focus Specs/Suites

Prefix with `f` or use `focused=true` — ONLY focused specs/suites run:

```boxlang
// Focus a single spec
fit( "is the only spec that runs", () => {
    expect( true ).toBeTrue()
} )

// Focus a suite (all children run)
fdescribe( "Critical path", () => {
    it( "step one", () => { ... } )
    it( "step two", () => { ... } )
} )
```

> **Warning**: Never commit focused tests — CI will only run those specs.

---

## Mocking with MockBox

MockBox is built into TestBox. Helper methods are available in every bundle.

### Creating Mocks

```boxlang
// Create a mock of a class (real methods unless overridden)
var mockService = createMock( "services.UserService" )

// Create an empty mock (ALL methods removed — you mock everything)
var mockRepo = createEmptyMock( "repositories.UserRepository" )

// Decorate an existing instance with mocking capabilities
var realService = new services.UserService()
prepareMock( realService )

// Chainable setup
var mockUser = createMock( "model.User" )
    .$( "isActive", true )
    .$( "getRole", "admin" )
    .$( "getName", "Luis Majano" )
```

### Mocking Methods: `$()`

```boxlang
// Return a single value
mockRepo.$( "findById", { id: 1, name: "Luis" } )

// Return nothing (void)
mockRepo.$( "deleteById" )

// Throw an exception
mockRepo.$( "findById",
    throwException = true,
    throwType       = "NotFoundException",
    throwMessage    = "Record not found"
)

// Use a callback for dynamic responses
mockRepo.$( method="findById", callback=( id ) => {
    if ( id == 1 ) return { id: 1, name: "Luis" }
    return javaCast( "null", "" )
} )
```

### Sequenced Return Values: `$results()`

Returns values in sequence, cycling when exhausted:

```boxlang
// First call → true, second call → false, third → true (repeats)
mockSession.$( "isLoggedIn" ).$results( true, false )

expect( mockSession.isLoggedIn() ).toBeTrue()   // 1st call
expect( mockSession.isLoggedIn() ).toBeFalse()  // 2nd call
expect( mockSession.isLoggedIn() ).toBeTrue()   // 3rd call (cycles)
```

### Argument-Specific Mocking: `$args()`

Return different values based on what arguments were passed:

```boxlang
mockCache.$( "get" )
    .$args( "user-1" ).$results( { id: 1, name: "Luis" } )
    .$args( "user-2" ).$results( { id: 2, name: "Alexia" } )
    .$args( "missing" ).$results( javaCast( "null", "" ) )
```

### Property Mocking: `$property()`

```boxlang
// Mock a property on the object
mockService.$property( "timeout", "variables", 5000 )
mockService.$property( "cache", "instance", mockCache )
```

### Query Simulation: `$querySim()`

```boxlang
var mockQuery = mockDAO.$querySim(
    "id, name, email
     1  | Luis Majano   | luis@example.com
     2  | Alexia Majano | alexia@example.com"
)
mockDAO.$( "getUsers", mockQuery )
```

### Verification Methods

Check how and how many times mocked methods were called:

```boxlang
// Get call count
mockRepo.$count( "findById" )   // calls to findById specifically
mockRepo.$count()               // total calls to all mocked methods

// Assert call counts
expect( mockRepo.$times( 1, "findById" ) ).toBeTrue()  // called exactly once
expect( mockRepo.$once( "save" ) ).toBeTrue()          // alias for $times(1)
expect( mockRepo.$never( "delete" ) ).toBeTrue()       // never called
expect( mockRepo.$atLeast( 2, "findById" ) ).toBeTrue()
expect( mockRepo.$atMost( 3, "findById" ) ).toBeTrue()

// Inspect call log (arguments per call)
var log = mockRepo.$callLog()
expect( log.findById[ 1 ].id ).toBe( 42 )  // 1st call, named arg "id"

// Reset all mock state
mockRepo.$reset()
```

### Full Mocking Example

```boxlang
class extends="testbox.system.BaseSpec" {

    function beforeAll() {
        // Create service under test with mocked dependencies
        variables.mockUserDAO   = createEmptyMock( "repositories.UserDAO" )
        variables.mockEmailSvc  = createEmptyMock( "services.EmailService" )
        variables.service       = createMock( "services.UserService" )
        variables.service.init( variables.mockUserDAO, variables.mockEmailSvc )
    }

    function run() {

        describe( "UserService.register()", () => {

            beforeEach( () => {
                variables.mockUserDAO.$reset()
                variables.mockEmailSvc.$reset()
            } )

            it( "creates user and sends welcome email", () => {
                var newUser = { id: 99, email: "new@example.com", name: "Test" }
                variables.mockUserDAO.$( "create", newUser )
                variables.mockEmailSvc.$( "sendWelcome" )

                var result = variables.service.register( "new@example.com", "Test" )

                expect( result ).toBe( newUser )
                expect( variables.mockUserDAO.$once( "create" ) ).toBeTrue()
                expect( variables.mockEmailSvc.$once( "sendWelcome" ) ).toBeTrue()
            } )

            it( "throws when email already exists", () => {
                variables.mockUserDAO.$( "existsByEmail", true )

                expect( () => variables.service.register( "exists@example.com", "Test" ) )
                    .toThrow( type="DuplicateEmailException" )
            } )

        } )

    }

}
```

---

## Mock Data Generation (`mockData()`)

Available in all bundles via the `cbMockData` module included with TestBox:

```boxlang
// Generate 5 fake user records
var users = mockData(
    $num         = 5,
    id           = "autoincrement",
    name         = "name",
    email        = "email",
    age          = "age",
    isActive     = "boolean",
    createdDate  = "datetime",
    bio          = "lorem:2"      // 2 paragraphs of lorem ipsum
)

// Random count
var items = mockData( $num = "rnd:5:20", title = "name" )

// Return as struct instead of array
var single = mockData(
    $returnType = "struct",
    $num        = 1,
    id          = "uuid",
    name        = "name",
    email       = "email"
)
```

### Available Mock Types

| Type | Description |
|---|---|
| `autoincrement` | Sequential integer starting from 1 |
| `uuid` | Random UUID |
| `name` | Full name |
| `fname` / `lname` | First / last name |
| `email` | Email address |
| `age` | Adult age (18–75) |
| `all_age` | Any age (1–100) |
| `boolean` | `true` or `false` |
| `boolean-digit` | `0` or `1` |
| `date` / `datetime` | Random date/datetime |
| `datetime-iso` | ISO 8601 datetime |
| `num` / `num:X` / `num:X:Y` | Random numbers |
| `lorem:N` | N paragraphs of lorem ipsum |
| `baconlorem:N` | N paragraphs of bacon lorem |
| `ipaddress` | IPv4 address |
| `imageurl` / `imageurl_https` | Image URL |
| `website` | Website URL |
| `phone` | Phone number |
| `oneof:a:b:c` | One of the specified values |

---

## Async Testing

Run all specs in a suite concurrently using `asyncAll=true`:

```boxlang
describe( title="Parallel specs", asyncAll=true, body=() => {

    beforeEach( () => {
        variables.service = new MyService()
    } )

    it( "spec one", () => {
        expect( variables.service.ping() ).toBeTrue()
    } )

    it( "spec two", () => {
        expect( variables.service.version() ).notToBeEmpty()
    } )

} )
```

> **Warning**: In async mode, all variables must be properly `var`-scoped or
> thread-safe. Do NOT use shared `variables` scope when running async specs.

---

## Running Tests

### BoxLang CLI Runner

```bash
# Run all tests (default: tests/specs)
./testbox/run

# Run a specific bundle (dot notation)
./testbox/run tests.specs.UserServiceSpec

# Run all specs in a directory
./testbox/run --directory=tests.specs.unit

# Specify a bundle pattern
./testbox/run --bundles-pattern=*Spec*.bx

# Stream results in real-time (great for CI)
./testbox/run --stream

# Stop on first failure
./testbox/run --eager-failure

# Verbose output (every spec printed)
./testbox/run --verbose
```

### CLI Runner Options

| Option | Default | Description |
|---|---|---|
| `--bundles` | `*` | Dot-notation bundle list |
| `--bundles-pattern` | `*Spec*.bx\|*Test*.bx\|...` | Glob pattern |
| `--directory` | `tests.specs` | Directory to scan |
| `--recurse` | `true` | Recurse into subdirectories |
| `--eager-failure` | `false` | Stop on first failure |
| `--verbose` | `false` | Print each spec as it runs |
| `--stream` | `false` | Real-time streaming output |

### CommandBox Runner

```bash
box testbox run
box testbox run bundles=tests.specs.UserServiceSpec
box testbox run --verbose
```

---

## Test Harness Layout

```
tests/
├── specs/
│   ├── unit/
│   │   ├── UserServiceSpec.bx
│   │   └── OrderServiceSpec.bx
│   ├── integration/
│   │   └── UserFlowSpec.bx
│   └── resources/
│       └── fixtures.json
├── Application.bx        ← configures mappings for tests
└── runner.bxm            ← web runner template
```

### Minimal `Application.bx` for Tests

```boxlang
class {
    this.name              = "MyAppTests"
    this.mappings[ "/testbox" ] = expandPath( "/testbox" )
    this.mappings[ "/app" ]     = expandPath( "../src" )
}
```

---

## Common Patterns

### Testing Classes with Dependencies (Constructor Injection)

```boxlang
function beforeAll() {
    variables.mockRepo    = createEmptyMock( "UserRepository" )
    variables.mockMailer  = createEmptyMock( "Mailer" )
    variables.service     = new UserService( variables.mockRepo, variables.mockMailer )
}
```

### Testing Private Methods via `prepareMock`

```boxlang
it( "validates email format internally", () => {
    var svc = prepareMock( new UserService() )
    // expose private method for targeted testing
    expect( svc.validateEmail( "bad-email" ) ).toBeFalse()
    expect( svc.validateEmail( "good@email.com" ) ).toBeTrue()
} )
```

### Data-Driven Specs

```boxlang
var validInputs = [ "hello", "world", "boxlang" ]

for ( var word in validInputs ) {
    it(
        title = "validates '#word#' as a valid slug",
        data  = { word = word },
        body  = ( data ) => {
            expect( SlugValidator.isValid( data.word ) ).toBeTrue()
        }
    )
}
```

### Testing Query Results

```boxlang
it( "returns paginated user query", () => {
    var mockQ = mockDAO.$querySim(
        "id, name
         1  | Luis
         2  | Alexia"
    )
    mockDAO.$( "paginate", mockQ )

    var result = variables.service.getPage( 1, 10 )
    expect( result ).toBeQuery()
    expect( result.recordCount ).toBe( 2 )
} )
```

### Resetting Mocks Between Tests

Always reset mocks in `beforeEach` to avoid state bleed:

```boxlang
beforeEach( () => {
    variables.mockRepo.$reset()
    variables.mockMailer.$reset()
} )
```

---

## Anti-Patterns to Avoid

| Anti-Pattern | Better Approach |
|---|---|
| Testing implementation details (private internals) | Test public behaviour; use `prepareMock` sparingly |
| Shared mutable state between specs | Reset in `beforeEach`, use `afterEach` to clean up |
| `fit()` / `fdescribe()` committed to version control | Use focused specs only locally; CI must run all |
| One assertion per spec when they are naturally grouped | Group related assertions in one spec; split logically unrelated specs  |
| Not resetting mock call logs between specs | Call `$reset()` in `beforeEach` |
| Using `asyncAll=true` with shared `variables` scope | Ensure all state is `var`-scoped in async suites |
| Catch blocks swallowing exceptions in tests | Let exceptions propagate; rely on `toThrow()` |