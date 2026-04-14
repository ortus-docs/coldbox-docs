---
description: General-purpose delegates available in any WireBox-managed application
---

# Core Delegates

Core delegates are shipped with WireBox and are always available via the `@coreDelegates` namespace — no ColdBox required.

{% hint style="success" %}
For a deep dive into how WireBox delegation works (annotations, prefixes, suffixes, targeted methods), see the [WireBox Delegators](https://wirebox.ortusbooks.com/usage/wirebox-delegators) documentation.
{% endhint %}

## Available Core Delegates

| WireBox ID                   | Class Path                                      | Description                                              |
| ---------------------------- | ----------------------------------------------- | -------------------------------------------------------- |
| `Async@coreDelegates`        | `coldbox.system.core.delegates.Async`           | Async/parallel programming via the ColdBox AsyncManager  |
| `DateTime@coreDelegates`     | `coldbox.system.core.delegates.DateTime`        | Date and time utilities via DateTimeHelper               |
| `Env@coreDelegates`          | `coldbox.system.core.delegates.Env`             | Java system properties and OS environment variables      |
| `Flow@coreDelegates`         | `coldbox.system.core.delegates.Flow`            | Fluent flow-control methods for expressive chaining      |
| `JsonUtil@coreDelegates`     | `coldbox.system.core.delegates.JsonUtil`        | JSON serialization utilities                             |
| `Population@coreDelegates`   | `coldbox.system.core.delegates.Population`      | Object population from structs, JSON, XML, and queries   |
| `StringUtil@coreDelegates`   | `coldbox.system.core.delegates.StringUtil`      | String manipulation and formatting utilities             |

---

## Async

**WireBox ID:** `Async@coreDelegates`

Provides access to the ColdBox `AsyncManager`. Delegates `newFuture()` and `arrayRange()` directly; also exposes `async()` to retrieve the `AsyncManager` instance.

| Method            | Description                                          |
| ----------------- | ---------------------------------------------------- |
| `async()`         | Returns the `AsyncManager` instance                  |
| `newFuture(...)` | Creates and returns a new `Future` object            |
| `arrayRange(...)` | Creates a ranged parallel stream from an array       |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Async@coreDelegates" {

    function process() {
        var result = newFuture( () => expensiveOperation() ).get()
        arrayRange( [1,2,3], ( item ) => processItem( item ) )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Async@coreDelegates" {

    function process() {
        var result = newFuture( function() { return expensiveOperation() } ).get()
        arrayRange( [1,2,3], function( item ) { return processItem( item ) } )
    }

}
```
{% endtab %}
{% endtabs %}

---

## DateTime

**WireBox ID:** `DateTime@coreDelegates`

Delegates **all** public methods from `coldbox.system.async.time.DateTimeHelper` directly onto your class.

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="DateTime@coreDelegates" {

    function isExpired( required date expiresAt ) {
        return expiresAt.isBefore( now() )
    }

    function getNextWeek() {
        return now().plusDays( 7 )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="DateTime@coreDelegates" {

    function isExpired( required date expiresAt ) {
        return expiresAt.isBefore( now() )
    }

    function getNextWeek() {
        return now().plusDays( 7 )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Env

**WireBox ID:** `Env@coreDelegates`

Reads Java system properties and OS environment variables. Methods search Java properties first, then OS env vars, unless specified otherwise.

{% hint style="warning" %}
In ColdBox 8+, the old `getSystemSetting()`, `getSystemProperty()`, and `getEnv()` utility methods were **removed** from the framework supertype and replaced by this delegate.
{% endhint %}

| Method                                   | Description                                                                                                    |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `getSystemSetting( key, defaultValue )`  | Returns value from **Java system properties first, then OS env vars**. Throws `SystemSettingNotFound` if missing and no default given. |
| `getSystemProperty( key, defaultValue )` | Returns a **Java system property** (`-Dkey=value`) only.                                                       |
| `getEnv( key, defaultValue )`            | Returns an **OS environment variable** only.                                                                   |
| `getJavaSystem()`                        | Returns the raw `java.lang.System` instance.                                                                   |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Env@coreDelegates" {

    function getDatabaseUrl() {
        return getSystemSetting( "DB_URL", "jdbc:h2:mem:testdb" )
    }

    function getPort() {
        // Required — throws SystemSettingNotFound if missing
        return getEnv( "SERVER_PORT" )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Env@coreDelegates" {

    function getDatabaseUrl() {
        return getSystemSetting( "DB_URL", "jdbc:h2:mem:testdb" )
    }

    function getPort() {
        // Required — throws SystemSettingNotFound if missing
        return getEnv( "SERVER_PORT" )
    }

}
```
{% endtab %}
{% endtabs %}

---

## Flow

**WireBox ID:** `Flow@coreDelegates`

Fluent flow-control methods. All methods return the parent object so calls can be chained.

| Method                                         | Description                                                                              |
| ---------------------------------------------- | ---------------------------------------------------------------------------------------- |
| `peek( target )`                               | Executes a closure with the parent as its argument; returns the parent for chaining.     |
| `when( target, success, failure )`             | Runs `success` if `target` is truthy; `failure` if falsy (optional).                    |
| `unless( target, success, failure )`           | Runs `success` if `target` is falsy; `failure` if truthy (optional).                    |
| `throwIf( target, type, message, detail )`     | Throws an exception if `target` is `true`.                                               |
| `throwUnless( target, type, message, detail )` | Throws an exception if `target` is `false`.                                              |
| `ifNull( target, success, failure )`           | Runs `success` if `target` is `null`; `failure` if not null (optional).                 |
| `ifPresent( target, success, failure )`        | Runs `success` if `target` is **not** `null`; `failure` if null (optional).             |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Flow@coreDelegates" {

    function save( required struct data ) {
        return this
            .throwIf( data.isEmpty(), "ValidationException", "Data cannot be empty" )
            .when( data.keyExists( "email" ), () => validateEmail( data.email ) )
            .peek( (self) => logBox.getRootLogger().info( "Saving #data.name#" ) )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Flow@coreDelegates" {

    function save( required struct data ) {
        return this
            .throwIf( data.isEmpty(), "ValidationException", "Data cannot be empty" )
            .when( data.keyExists( "email" ), function() { validateEmail( data.email ) } )
            .peek( function( self ) { logBox.getRootLogger().info( "Saving #data.name#" ) } )
    }

}
```
{% endtab %}
{% endtabs %}

---

## JsonUtil

**WireBox ID:** `JsonUtil@coreDelegates`

JSON serialization utilities. `toJson`, `prettyJson`, and `toPrettyJson` are delegated from the framework's core `Util` class.

| Method                 | Description                                                                                            |
| ---------------------- | ------------------------------------------------------------------------------------------------------ |
| `toJson( data )`       | Serializes any data to a JSON string.                                                                  |
| `prettyJson( data )`   | Serializes to an indented, human-readable JSON string.                                                 |
| `toPrettyJson( data )` | Alias for `prettyJson()`.                                                                              |
| `forAttribute( data )` | Serializes to JSON (if complex) then encodes for safe use inside an HTML attribute.                    |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="JsonUtil@coreDelegates" {

    function toApiResponse( required struct data ) {
        return {
            raw       : toJson( data ),
            pretty    : prettyJson( data ),
            attribute : forAttribute( data )
        }
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="JsonUtil@coreDelegates" {

    function toApiResponse( required struct data ) {
        return {
            raw       : toJson( data ),
            pretty    : prettyJson( data ),
            attribute : forAttribute( data )
        }
    }

}
```
{% endtab %}
{% endtabs %}

---

## Population

**WireBox ID:** `Population@coreDelegates`

Populates object properties from structs, JSON strings, XML, or query rows using the WireBox Object Populator. The `target` argument defaults to `$parent` (the delegating object itself).

{% hint style="info" %}
If you need to populate an object **from the current HTTP request collection**, use [`Population@cbDelegates`](coldbox-delegates.md#population) instead — it automatically defaults `memento` to the request collection.
{% endhint %}

| Method                                       | Description                                    |
| -------------------------------------------- | ---------------------------------------------- |
| `populate( memento, ... )`                   | Populate from a struct/map.                    |
| `populateWithPrefix( memento, prefix, ... )` | Populate using prefixed keys.                  |
| `populateFromJSON( JSONString, ... )`        | Populate from a JSON string.                   |
| `populateFromXML( xml, root, ... )`          | Populate from an XML string or XML object.     |
| `populateFromQuery( qry, rowNumber, ... )`   | Populate from a specific query row.            |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="Population@coreDelegates" {

    function update( required struct data ) {
        return populate( memento: data, ignoreEmpty: true )
    }

    function updateFromJson( required string json ) {
        return populateFromJSON( JSONString: json, trustedSetter: true )
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="Population@coreDelegates" {

    function update( required struct data ) {
        return populate( memento: data, ignoreEmpty: true )
    }

    function updateFromJson( required string json ) {
        return populateFromJSON( JSONString: json, trustedSetter: true )
    }

}
```
{% endtab %}
{% endtabs %}

---

## StringUtil

**WireBox ID:** `StringUtil@coreDelegates`

String manipulation and formatting utilities.

| Method                             | Description                                                                           |
| ---------------------------------- | ------------------------------------------------------------------------------------- |
| `prettySql( target )`              | Formats a SQL string with newlines and indentation for readability.                   |
| `slugify( str, maxLength, allow )` | Creates a URL-safe slug. `maxLength=0` means no limit.                                |
| `camelCase( target )`              | Converts `snake_case` or `kebab-case` to `camelCase`.                                 |
| `headline( target )`               | Converts a delimited or cased string to `Title Case With Spaces`.                    |
| `ucFirst( target )`                | Uppercases the first character of a string.                                           |
| `lcFirst( target )`                | Lowercases the first character of a string.                                           |
| `kebabCase( target )`              | Converts a string to `kebab-case`.                                                    |
| `snakeCase( target, delimiter )`   | Converts a string to `snake_case` (delimiter defaults to `_`).                       |
| `pluralize( word )`                | Returns the plural form of an English word.                                           |
| `singularize( word )`              | Returns the singular form of an English word.                                         |

{% tabs %}
{% tab title="BoxLang" %}
```bx
class delegates="StringUtil@coreDelegates" {

    function getPageSlug( required string title ) {
        return slugify( title, 100 )
        // "My Cool Post!" -> "my-cool-post"
    }

    function getTableName( required string modelName ) {
        return pluralize( snakeCase( modelName ) )
        // "UserProfile" -> "user_profiles"
    }

}
```
{% endtab %}
{% tab title="CFML" %}
```cfscript
component delegates="StringUtil@coreDelegates" {

    function getPageSlug( required string title ) {
        return slugify( title, 100 )
        // "My Cool Post!" -> "my-cool-post"
    }

    function getTableName( required string modelName ) {
        return pluralize( snakeCase( modelName ) )
        // "UserProfile" -> "user_profiles"
    }

}
```
{% endtab %}
{% endtabs %}
