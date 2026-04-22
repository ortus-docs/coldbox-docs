---
name: boxlang-runtime-jsr-223
description: "Use this skill when embedding BoxLang into Java applications via the JSR-223 scripting API, evaluating BoxLang code from Java, sharing data between Java and BoxLang via Bindings, building rules engines, template processors, or configurable logic using BoxLang as a scripting engine inside a JVM application."
---

# BoxLang JSR-223 Scripting

## Overview

JSR-223 ("Scripting for the Java Platform") allows Java applications to embed
BoxLang as a scripting engine. This enables dynamic business logic, configurable
rules, template processing, and runtime extensibility without a full recompile.

---

## Dependencies

### Maven

```xml
<dependency>
    <groupId>io.boxlang</groupId>
    <artifactId>boxlang</artifactId>
    <version>1.10.1</version>
</dependency>
```

### Gradle

```groovy
dependencies {
    implementation 'io.boxlang:boxlang:1.10.1'
}
```

**Requirement:** Java 21+

---

## Quick Start

```java
import javax.script.*;

// Get BoxLang engine by name
ScriptEngine engine = new ScriptEngineManager().getEngineByName( "BoxLang" );

// Execute BoxLang code
Object result = engine.eval( "return 'Hello from BoxLang!'" );
System.out.println( result );  // Hello from BoxLang!
```

---

## Passing Data to BoxLang

Use `Bindings` to pass Java objects into the BoxLang execution context:

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName( "BoxLang" );

Bindings bindings = engine.createBindings();
bindings.put( "name",  "Java Developer" );
bindings.put( "items", java.util.Arrays.asList( "Spring", "Maven", "BoxLang" ) );

Object result = engine.eval( """
    message   = "Hello " & name & "!"
    itemCount = items.len()
    return {
        greeting:    message,
        totalItems:  itemCount,
        technologies: items.map( ( item ) => item.uCase() )
    }
""", bindings );

System.out.println( result );
```

---

## Retrieving Values from BoxLang

BoxLang can set values back into the Bindings scope for Java to read:

```java
Bindings bindings = engine.createBindings();
bindings.put( "inputData", myData );

engine.eval( """
    processed    = inputData.map( ( item ) -> item * 2 )
    errorCount   = 0
    successCount = processed.len()
""", bindings );

// Read values back from the scope
Object processed = bindings.get( "processed" );
int successCount = (int) bindings.get( "successCount" );
```

---

## Rules Engine Pattern

Embed BoxLang for business rules that non-developers can modify:

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName( "BoxLang" );

// Load rules from a file, database, or config — not hardcoded Java
String discountRules = Files.readString( Path.of( "rules/discount.bxs" ) );

Bindings ctx = engine.createBindings();
ctx.put( "customer", customerData );
ctx.put( "order",    orderData );

Boolean eligible = (Boolean) engine.eval( discountRules, ctx );
```

`rules/discount.bxs`:
```boxlang
// Business rules — editable without recompiling Java
if ( order.total > 1000 && customer.tier == "GOLD" ) {
    return true
}
if ( customer.loyaltyPoints > 5000 ) {
    return true
}
return false
```

---

## Template Processing Pattern

Use BoxLang string interpolation as a lightweight template engine:

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName( "BoxLang" );

String template = Files.readString( Path.of( "templates/email.bxs" ) );

Bindings data = engine.createBindings();
data.put( "firstName", "Alice" );
data.put( "orderNumber", "ORD-12345" );
data.put( "total",       99.99 );

String rendered = (String) engine.eval( template, data );
System.out.println( rendered );
```

`templates/email.bxs`:
```boxlang
return """
Dear #firstName#,

Your order #orderNumber# totaling $#numberFormat( total, "0.00" )# has been confirmed.

Thank you for your business!
"""
```

---

## Performance: Reuse the Engine

`ScriptEngineManager` creates a new runtime per call. For production use,
create the engine once and reuse it:

```java
// Application startup — create engine once
private static final ScriptEngine BOXLANG_ENGINE =
    new ScriptEngineManager().getEngineByName( "BoxLang" );

// Per-request — create fresh Bindings but reuse the engine
public Object evaluate( String script, Map<String, Object> vars ) throws ScriptException {
    Bindings bindings = BOXLANG_ENGINE.createBindings();
    bindings.putAll( vars );
    return BOXLANG_ENGINE.eval( script, bindings );
}
```

---

## BoxLang Home Configuration

Control where BoxLang stores its runtime files (classes, modules, config):

```java
// Set BOXLANG_HOME before creating the engine manager
System.setProperty( "boxlang.home", "/opt/myapp/.boxlang" );
ScriptEngine engine = new ScriptEngineManager().getEngineByName( "BoxLang" );
```

Or via environment variable before JVM startup:

```bash
export BOXLANG_HOME=/opt/myapp/.boxlang
java -jar myapp.jar
```

---

## Loading BoxLang Modules

Modules extend BoxLang with BIFs, components, and capabilities:

```java
// Using system property
System.setProperty( "boxlang.home", "/opt/myapp/.boxlang" );
// Then install modules to /opt/myapp/.boxlang/modules/ via box CLI
// box install bx-mail --boxlang-home=/opt/myapp/.boxlang
```

Or set in `boxlang.json` in the home directory:

```json5
{
    "modules": {
        "load": [ "bx-mail", "bx-pdf" ]
    }
}
```

---

## Exception Handling

BoxLang exceptions are wrapped in `ScriptException`. Unwrap to inspect:

```java
try {
    engine.eval( script, bindings );
} catch ( ScriptException e ) {
    // e.getCause() may be a BoxLang runtime exception
    Throwable cause = e.getCause();
    if ( cause != null ) {
        System.err.println( "BoxLang error: " + cause.getMessage() );
    } else {
        System.err.println( "Script error: " + e.getMessage() );
    }
}
```

---

## Core JSR-223 Classes Reference

| Class | Purpose |
|-------|---------|
| `ScriptEngineManager` | Discovers and creates script engines |
| `ScriptEngine` | Executes scripts, manages state |
| `Bindings` | Key-value map for passing data to/from scripts |
| `ScriptContext` | Full context (global + engine-level Bindings) |
| `Compilable` | Compile scripts for repeated execution (if supported) |
| `Invocable` | Call specific functions defined in the engine |

---

## Production Checklist

- [ ] Engine created once and reused (not per-request)
- [ ] Fresh `Bindings` per execution to prevent state leakage
- [ ] Scripts loaded from trusted sources only (not user-supplied)
- [ ] Exceptions caught and wrapped; `ScriptException.getCause()` inspected
- [ ] `BOXLANG_HOME` set to a writable, isolated directory
- [ ] Module dependencies pre-installed to the BoxLang home