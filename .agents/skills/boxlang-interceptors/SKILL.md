---
name: boxlang-interceptors
description: "Use this skill when working with BoxLang's interceptor/event system: creating interceptors, registering announcement points, announcing custom events with announce() or announceAsync(), building pre/post operation hooks, validation interceptors, security guards, or leveraging BoxRegisterInterceptor() BIF for standalone and module-based interception."
---

# BoxLang Interceptors

## Overview

BoxLang interceptors provide Aspect-Oriented Programming (AOP) capabilities through
an event-driven announcement system. Interceptors listen to named events and execute
cross-cutting logic — logging, security, caching, validation — without coupling
that logic to business code. Priority controls execution order; async announcements
run in background threads.

## Creating an Interceptor

```boxlang
/**
 * interceptors/RequestLogger.bx
 */
class {

    function onRequestStart( struct interceptData ) {
        request.startTime = getTickCount()
        writeLog( text: "Request started: #cgi.script_name#", type: "information" )
    }

    function onRequestEnd( struct interceptData ) {
        var duration = getTickCount() - request.startTime
        writeLog( text: "Completed in #duration#ms: #cgi.script_name#", type: "information" )
    }
}
```

## Registering Interceptors

### Application.bx (application-scoped)

```boxlang
class {
    this.name = "MyApp"

    this.interceptors = [
        { class: "interceptors.RequestLogger" },
        { class: "interceptors.SecurityCheck", priority: 1 },
        { class: "interceptors.CacheWarmer",   priority: 10 }
    ]

    function onApplicationStart() {
        announce( "onApplicationStart", { timestamp: now() } )
    }
}
```

### Programmatic Registration (BIF)

```boxlang
// Register at runtime using BoxRegisterInterceptor()
BoxRegisterInterceptor( "interceptors.RequestLogger" )

// With named options
BoxRegisterInterceptor(
    class: "interceptors.SecurityCheck",
    priority: 1,
    properties: { secret: getSetting( "JWT_SECRET" ) }
)
```

## Announcing Events

```boxlang
// Synchronous — all listeners execute before returning
announce( "userCreated", {
    userID: newUser.id,
    email: newUser.email,
    timestamp: now()
} )

// Asynchronous — runs in a background thread
announceAsync( "emailQueued", {
    to: user.email,
    subject: "Welcome"
} )
```

## Common Interceptor Patterns

### Pre / Post Operation Hooks

```boxlang
class DataSanitizer {

    /** Sanitize data before save */
    function preDataSave( interceptData ) {
        var data = interceptData.data

        data.each( ( key, value ) -> {
            if ( isSimpleValue( value ) ) data[key] = trim( value )
        } )

        if ( data.keyExists( "description" ) ) {
            data.description = htmlEditFormat( data.description )
        }
    }

    /** Clear cache after save */
    function postDataSave( interceptData ) {
        cacheRemove( "#interceptData.entityName#_#interceptData.entityID#" )
    }
}
```

### Validation Guard

```boxlang
class ValidationInterceptor {

    property name="validationService" inject="ValidationService"

    function preEntitySave( interceptData ) {
        var result = validationService.validate( interceptData.entity )

        if ( result.hasErrors() ) {
            interceptData.abortProcessing = true
            throw(
                type: "ValidationException",
                message: "Validation failed",
                detail: serializeJSON( result.getAllErrors() )
            )
        }
    }
}
```

### Security Guard

```boxlang
class SecurityInterceptor {

    property name="authService" inject="AuthService"

    function preHandler( interceptData ) {
        // Skip public areas
        if ( listFindNoCase( "home,auth", interceptData.handler ) ) return

        if ( !authService.isLoggedIn() ) {
            interceptData.event.overrideEvent( "auth.login" )
            interceptData.abortProcessing = true
        }
    }
}
```

### Async Background Processing

```boxlang
class AsyncProcessor {

    /** Kick off work asynchronously */
    function onEmailQueued( interceptData ) {
        announceAsync( "processEmail", {
            to:      interceptData.to,
            subject: interceptData.subject,
            body:    interceptData.body
        } )
    }

    /** Worker — runs in background thread */
    function onProcessEmail( interceptData ) {
        mailService.send(
            to:      interceptData.to,
            subject: interceptData.subject,
            body:    interceptData.body
        )
    }
}
```

### Chain of Responsibility with Priority

```boxlang
// Lower number = runs earlier
this.interceptors = [
    { class: "interceptors.RequestLogger", priority: 1  },
    { class: "interceptors.AuthCheck",     priority: 2  },
    { class: "interceptors.RateLimiter",   priority: 3  }
]
```

## Interceptor State

```boxlang
class RequestTracker {

    variables.requestMap = {}

    function onRequestStart( interceptData ) {
        var id = createUUID()
        variables.requestMap[id] = { startTime: getTickCount(), path: cgi.script_name }
        interceptData.requestID = id
    }

    function onRequestEnd( interceptData ) {
        var id   = interceptData.requestID
        var data = variables.requestMap[id]
        writeLog( "Request #id# done in #getTickCount() - data.startTime#ms" )
        structDelete( variables.requestMap, id )
    }
}
```

## Module Registration

Modules register interceptors in `ModuleConfig.bx`:

```boxlang
// ModuleConfig.bx
class {

    function configure() {
        interceptors = [
            { class: "#moduleMapping#.interceptors.MyInterceptor", priority: 5 }
        ]
    }
}
```

## Best Practices

- **Single responsibility** — one interceptor, one concern
- **Keep interceptors fast** — move slow work to `announceAsync()`
- **Always `try/catch`** inside handlers — a thrown exception inside `onUserCreated` should never break user creation
- **Conditional early return** — skip irrelevant entities or events at the top of the method
- **Priority** — register security interceptors at priority 1 (lowest number), logging at high numbers
- **Avoid recursion** — never announce the same event you are listening to

```boxlang
// ✅ Fast: log then hand off heavy work
function onUserCreated( interceptData ) {
    writeLog( "User created: #interceptData.email#" )
    announceAsync( "sendWelcomeEmail", interceptData )
}

// ✅ Guard with early return
function onDataSave( interceptData ) {
    if ( interceptData.entityName != "User" ) return
    // user-specific logic
}

// ✅ Error-safe side effects
function onUserCreated( interceptData ) {
    try {
        sendEmail( interceptData.email )
    } catch ( any e ) {
        writeLog( "Email error: #e.message#" )
        // Do NOT rethrow — don't break user creation
    }
}

// ❌ Recursion: triggers same interceptor again
function onDataSave( interceptData ) {
    announce( "onDataSave", data )   // infinite loop
}
```