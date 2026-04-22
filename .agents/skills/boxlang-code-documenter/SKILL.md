---
name: boxlang-code-documenter
description: "Use this skill when adding documentation comments to BoxLang code: writing function/class Javadoc-style comments, documenting arguments, return types, exceptions, examples, or generating structured API reference documentation for BoxLang classes and BIFs. Comments written with these conventions are compatible with DocBox API documentation generation."
---

# BoxLang Code Documenter

## Overview

BoxLang supports Javadoc-style documentation comments. Documenting classes,
functions, and arguments enables IDE tooling, auto-generated API docs, and
serves as inline specification for future maintainers.

---

## Comment Styles

BoxLang supports three comment styles:

```boxlang
// Single-line comment

/* Multi-line
   comment */

/**
 * Documentation comment (Javadoc-style)
 * Used for classes, functions, and components.
 */
```

---

## Class Documentation

Place the doc comment immediately above the `class` keyword:

```boxlang
/**
 * UserService handles all user lifecycle operations including creation,
 * authentication, profile management, and deactivation.
 *
 * @author Jane Smith
 * @since 2.0.0
 * @see UserRepository
 */
class accessors="true" {

    property name="userRepo"  inject="UserRepository"
    property name="emailSvc"  inject="EmailService"

}
```

---

## Function Documentation

Document every public function. Place the comment directly above the function:

```boxlang
/**
 * Retrieves a user by their unique identifier.
 *
 * Returns `null` if no user matching `id` is found.
 * Throws `UserNotFoundException` if `throwOnMissing` is true.
 *
 * @param  id              The unique numeric ID of the user to retrieve.
 * @param  throwOnMissing  When true, throws instead of returning null. Default: false.
 *
 * @return                 A Struct containing user data, or null.
 *
 * @throws UserNotFoundException  When throwOnMissing=true and user does not exist.
 *
 * @example
 * var user = userService.getById( 42 )
 * if ( !isNull( user ) ) {
 *     println( user.name )
 * }
 */
struct function getById(
    required numeric id,
    boolean throwOnMissing = false
) {
    var user = variables.userRepo.find( arguments.id )

    if ( isNull( user ) && arguments.throwOnMissing ) {
        throw( type="UserNotFoundException", message="User #arguments.id# not found" )
    }

    return user
}
```

---

## Core Block Tags

| Tag | Purpose | Example |
|-----|---------|---------|
| `@param name` | Document an argument | `@param userId  The user's ID.` |
| `@return` | Document the return value | `@return A Struct of user data.` |
| `@throws ExType` | Document thrown exceptions | `@throws AuthException When token invalid.` |
| `@example` | Provide a usage example | See above |
| `@since version` | When added | `@since 1.5.0` |
| `@version` | Component/function version | `@version 2.1.0` |
| `@deprecated` | Mark as deprecated | `@deprecated Use getUserV2() instead.` |
| `@see RefName` | Cross-reference (DocBox: not yet implemented) | `@see UserRepository` |
| `@author name` | File/function author | `@author John Doe` |
| `@{anything}` | Custom metadata — DocBox documents any block pair | `@license MIT` |

---

## Argument Sub-Annotations

DocBox supports dot-notation to attach additional metadata to a specific argument.
Use `@argName.tagName value` to add attributes to a documented argument:

```boxlang
/**
 * Get Java FileInputStream for resource bundle.
 *
 * @rbFilePath       Path + filename for resource, including locale + .properties
 * @rbFilePath.deprecated  true
 *
 * @return java.io.FileInputStream
 * @throws ResourceBundle.InvalidBundlePath
 */
public function getResourceFileInputStream( required string rbFilePath ) {
}
```

---

## @doc.type — Generic Type Annotations

Use `@doc.type` to specify generic types for return values and arguments when the
declared type is `array`, `struct`, or `any`. DocBox renders these as typed generics.

### Return Type Generics

```boxlang
/**
 * Gets all active users.
 *
 * @return Array of User objects
 * @doc.type Array<User>
 */
public array function getActiveUsers() { }

/**
 * Gets user preferences as a configuration map.
 *
 * @return Struct mapping setting names to values
 * @doc.type Struct<String,Any>
 */
public struct function getUserPreferences() { }
```

### Argument Type Generics

```boxlang
/**
 * Processes a batch of user records.
 *
 * @users  Array of User objects to process
 * @users.doc.type Array<User>
 */
public void function processBatch( required array users ) { }

/**
 * Updates user settings.
 *
 * @settings  Map of setting names to their values
 * @settings.doc.type Struct<String,Any>
 */
public void function updateSettings( required struct settings ) { }
```

### Inline Generic Annotations (BoxLang)

```boxlang
// Return type inline
public array function getUsers() doc.type="Array<User>" { }

// Parameter inline
public void function setCache(
    required struct cache doc.type="String,Any"
) { }
```

### Complex/Nested Generics

```boxlang
/**
 * Gets a map of user IDs to their roles.
 *
 * @doc.type Struct<Numeric,Array<String>>
 */
public struct function getUserRoles() { }
```

---

## Property Documentation

Document class properties, especially for public API classes:

```boxlang
class accessors="true" {

    /** The user's unique identifier. Read-only after creation. */
    property name="id" type="numeric" setter="false"

    /** The user's display name. Maximum 100 characters. */
    property name="displayName" type="string"

    /**
     * The user's account status.
     * Valid values: "active", "suspended", "pending", "deleted"
     */
    property name="status" type="string" default="pending"

    /** ISO 8601 timestamp of when this account was created. */
    property name="createdAt" type="date" setter="false"

}
```

---

## Full Class Documentation Example

```boxlang
/**
 * OrderProcessor orchestrates the end-to-end order fulfillment workflow.
 *
 * Responsibilities:
 * - Validates order items and quantities
 * - Reserves inventory
 * - Processes payment
 * - Triggers fulfillment notifications
 *
 * Usage:
 * ```
 * var processor = new OrderProcessor( paymentGateway, inventoryService )
 * var result    = processor.process( order )
 * ```
 *
 * @author  Ortus Solutions
 * @since   3.0.0
 * @see     PaymentGateway
 * @see     InventoryService
 */
class {

    /** @param paymentGateway  Payment processing implementation. */
    /** @param inventoryService  Inventory management implementation. */
    function init(
        required PaymentGateway paymentGateway,
        required InventoryService inventoryService
    ) {
        variables.paymentGateway  = arguments.paymentGateway
        variables.inventoryService = arguments.inventoryService
        return this
    }

    /**
     * Processes a complete order from validation through payment.
     *
     * @param  order  A Struct containing items (Array), customerId (numeric), and shippingAddress (Struct).
     *
     * @return  A Struct with keys: success (boolean), orderId (numeric), message (string).
     *
     * @throws ValidationException  When the order structure is invalid.
     * @throws PaymentException     When payment processing fails.
     * @throws InventoryException   When items are out of stock.
     *
     * @example
     * var result = orderProcessor.process({
     *     customerId:      42,
     *     items:           [ { productId: 1, qty: 2 } ],
     *     shippingAddress: { street: "123 Main St", city: "Portland" }
     * })
     * if ( result.success ) {
     *     println( "Order #result.orderId# confirmed!" )
     * }
     */
    struct function process( required struct order ) {
        validateOrder( arguments.order )
        reserveInventory( arguments.order.items )
        var paymentResult = variables.paymentGateway.charge( arguments.order )
        return {
            success: true,
            orderId: paymentResult.transactionId,
            message: "Order confirmed"
        }
    }

    /**
     * Validates order structure and item availability.
     *
     * @param  order  The order struct to validate.
     *
     * @throws ValidationException  When order is missing required fields.
     *
     * @access private
     */
    private void function validateOrder( required struct order ) {
        if ( !structKeyExists( arguments.order, "customerId" ) ) {
            throw( type="ValidationException", message="customerId is required" )
        }
        if ( !structKeyExists( arguments.order, "items" ) || arguments.order.items.isEmpty() ) {
            throw( type="ValidationException", message="Order must contain at least one item" )
        }
    }

}
```

---

## Script File Documentation

For `.bxs` script files, document the purpose and usage at the top:

```boxlang
/**
 * generateReport.bxs
 *
 * Generates a monthly sales summary report and saves it to /reports/.
 *
 * Usage:
 *   boxlang generateReport.bxs [--month=YYYY-MM] [--output=/path/to/output]
 *
 * Arguments:
 *   --month   Month to report on (default: previous month). Format: YYYY-MM
 *   --output  Output directory (default: /var/reports/)
 *
 * @author   Ortus Solutions
 * @requires bx-pdf module for PDF export
 */

var month  = arguments[1] ?: dateFormat( dateAdd( "m", -1, now() ), "yyyy-mm" )
var output = arguments[2] ?: "/var/reports/"

// ... script logic
```

---

## Template File Documentation

For `.bxm` templates, add a brief comment header:

```html
<!---
    views/userProfile.bxm

    Displays the public profile page for a given user.

    Expected variables (set by controller):
    - user     (struct)   User data struct with name, bio, avatar
    - posts    (array)    Array of recent post structs
    - isOwner  (boolean)  Whether the current viewer owns this profile
--->
<bx:output>
<div class="profile">
    <h1>#encodeForHTML( user.name )#</h1>
</div>
</bx:output>
```

---

## Documentation Completeness Checklist

For every **public** function, ensure the doc comment includes:

- [ ] One-line summary (first line of comment)
- [ ] Expanded description if behavior is non-obvious
- [ ] `@param` for every argument (name + what it is)
- [ ] `@doc.type` when return or argument type is `array`, `struct`, or `any`
- [ ] `@return` describing what is returned (type + shape)
- [ ] `@throws` for every exception type that can escape
- [ ] `@example` for any non-trivial function
- [ ] `@since` for API versioning
- [ ] `@deprecated` + replacement note for deprecated functions

> **DocBox Integration:** All documentation comments written with these conventions
> are automatically parsed by [DocBox](../docbox/) to generate HTML, JSON, and UML
> API documentation. Run `boxlang module:docbox --source=/src --mapping=myapp --output-dir=/docs`
> to generate docs from your annotated code.