---
name: boxlang-security
description: "Use this skill when reviewing BoxLang code or applications for security vulnerabilities, configuring security settings, preventing injection attacks, handling file uploads safely, managing secrets, or applying secure coding patterns drawn from OWASP Top 10 and CFML/Java security history."
---

# BoxLang Security

## Overview

BoxLang inherits both the power and the historical attack surface of ColdFusion/CFML
and the JVM. This skill documents security patterns and runtime controls to help
build secure applications from the ground up.

---

## Runtime Security Configuration (`boxlang.json`)

```json5
"security": {
    // Regex patterns — prevent dangerous Java class access
    "disallowedImports": [
        "java\\.lang\\.(ProcessBuilder|Runtime)",
        "java\\.io\\.(FileWriter|PrintWriter)",
        "java\\.lang\\.reflect\\."
    ],
    // BIFs that should be disabled in production web apps
    "disallowedBifs": [
        "createObject",
        "systemExecute",
        "getSystemInfo"
    ],
    // Components that should be disabled in production
    "disallowedComponents": [
        "execute"
    ],
    // Prevent system properties from leaking into server scope
    "populateServerSystemScope": false,
    // Explicit upload whitelist (overrides disallowed list)
    "allowedFileOperationExtensions": [ "jpg", "png", "pdf", "docx" ],
    // Dangerous executable extensions blocked on file upload and copy/move
    "disallowedFileOperationExtensions": [
        "exe", "bat", "sh", "bx", "bxm", "bxs", "php", "jsp", "jar", "dll"
    ]
}
```

---

## Injection Prevention

### SQL Injection

**NEVER** build SQL with string concatenation. Always use QueryParam:

```boxlang
// BAD — SQL injection vulnerability
var result = queryExecute(
    "SELECT * FROM users WHERE email = '#email#'"
)

// GOOD — parameterized query (prevents SQL injection)
var result = queryExecute(
    "SELECT * FROM users WHERE email = :email",
    { email: { value: arguments.email, cfsqltype: "cf_sql_varchar" } }
)

// GOOD — struct shorthand
var result = queryExecute(
    "SELECT * FROM users WHERE id = :id AND status = :status",
    {
        id:     { value: arguments.id,     cfsqltype: "cf_sql_integer" },
        status: { value: arguments.status, cfsqltype: "cf_sql_varchar" }
    }
)
```

### Cross-Site Scripting (XSS)

Encode all user-supplied output in HTML context:

```boxlang
// BAD — raw user input rendered in HTML
<bx:output>#userComment#</bx:output>

// GOOD — HTML encode before output
<bx:output>#encodeForHTML( userComment )#</bx:output>

// For HTML attribute context
<input value="#encodeForHTMLAttribute( userInput )#">

// For JavaScript context
<script>var name = "#encodeForJavaScript( userName )#";</script>

// For URL context
<a href="/search?q=#encodeForURL( searchTerm )#">Search</a>
```

Use the right encoding function per context:

| Context | Function |
|---------|---------|
| HTML content | `encodeForHTML()` |
| HTML attributes | `encodeForHTMLAttribute()` |
| JavaScript strings | `encodeForJavaScript()` |
| URL parameters | `encodeForURL()` |
| CSS values | `encodeForCSS()` |

### Cross-Site Request Forgery (CSRF)

```boxlang
// Generate a token per session/form
var token = generateSecureToken()
session.csrfToken = token

// In the form template
<input type="hidden" name="csrfToken" value="#session.csrfToken#">

// Validate on POST
function onRequestStart( required string targetPage ) {
    if ( cgi.request_method == "POST" ) {
        if ( !structKeyExists( form, "csrfToken" ) ||
             form.csrfToken != session.csrfToken ) {
            throw( type="SecurityException", message="CSRF validation failed" )
        }
    }
    return true
}
```

---

## File Upload Security

Always validate file uploads before processing:

```boxlang
function handleUpload( required string fieldName ) {
    // Restrict to explicit allowed types and extensions
    var upload = fileUpload(
        destination    = getTempDirectory(),
        filefield      = arguments.fieldName,
        accept         = "image/jpeg,image/png,application/pdf",
        nameconflict   = "makeunique"
    )

    // Validate extension explicitly regardless of MIME type
    var allowedExtensions = [ "jpg", "jpeg", "png", "pdf" ]
    if ( !allowedExtensions.contains( lCase( upload.serverFileExt ) ) ) {
        fileDelete( upload.serverDirectory & "/" & upload.serverFile )
        throw( type="SecurityException", message="Disallowed file type" )
    }

    // Validate file size
    var maxSizeBytes = 5 * 1024 * 1024  // 5 MB
    if ( upload.fileSize > maxSizeBytes ) {
        fileDelete( upload.serverDirectory & "/" & upload.serverFile )
        throw( type="SecurityException", message="File too large" )
    }

    // Store outside the webroot, serve via handler — never serve raw uploads
    var safePath = expandPath( "/private/uploads/" ) & createUUID() & "." & upload.serverFileExt
    fileMove( upload.serverDirectory & "/" & upload.serverFile, safePath )

    return safePath
}
```

---

## Secrets Management

Never hardcode secrets. Use environment variables via the BoxLang config:

```json5
// boxlang.json — uses env var substitution
"datasources": {
    "mainDB": {
        "driver":   "postgresql",
        "host":     "${env.DB_HOST:localhost}",
        "username": "${env.DB_USERNAME:app}",
        "password": "${env.DB_PASSWORD}"   // No default — fail fast if missing
    }
}
```

In code, access via environment (not hardcoded):

```boxlang
// GOOD — read from environment
var apiKey    = server.system.environment.PAYMENT_API_KEY ?: ""
var jwtSecret = server.system.environment.JWT_SECRET ?: ""

if ( apiKey.isEmpty() ) {
    throw( type="ConfigException", message="PAYMENT_API_KEY is not configured" )
}
```

---

## Authentication Patterns

### Password Hashing

```boxlang
// GOOD — bcrypt-style hash (use bx-bcrypt module or GeneratePBKDFKey)
var salt   = generateSecureToken( 32 )
var hashed = hash( arguments.password & salt, "SHA-512" )

// Even better — use a dedicated BCrypt module
// install bx-bcrypt
var bcrypt  = new BCrypt()
var hashed  = bcrypt.hashpw( plainPassword, bcrypt.gensalt() )

// Verify
var isMatch = bcrypt.checkpw( loginPassword, storedHash )
```

### JWT / Token Validation

```boxlang
// Always validate before trusting any token data
function validateToken( required string token ) {
    try {
        // Use the bx-jwt module
        var claims = jwtService.decode( arguments.token )

        // Validate expiry
        if ( claims.exp < epochSecond() ) {
            throw( type="AuthException", message="Token expired" )
        }

        return claims
    } catch ( "JWT" e ) {
        throw( type="AuthException", message="Invalid token" )
    }
}
```

---

## Session Security

Configure session settings in `Application.bx`:

```boxlang
class {
    this.name              = "MyApp"
    this.sessionManagement = true
    this.sessionTimeout    = createTimeSpan( 0, 2, 0, 0 )  // 2 hours

    // Rotate session ID after login (session fixation prevention)
    function onLogin( required struct user ) {
        var oldData = duplicate( session )
        sessionRotate()  // BoxLang BIF to regenerate session ID
        structDelete( session, "ALL" )
        structAppend( session, oldData )
        session.userId    = user.id
        session.isLoggedIn = true
    }
}
```

---

## Path Traversal Prevention

Validate all user-supplied file paths:

```boxlang
function readFile( required string filename ) {
    var safeBase   = expandPath( "/app/uploads/" )
    var fullPath   = safeBase & arguments.filename
    var cleanPath  = createObject( "java", "java.io.File" ).init( fullPath ).getCanonicalPath()

    // Ensure the resolved path is within the allowed directory
    if ( !cleanPath.startsWith( safeBase ) ) {
        throw( type="SecurityException", message="Path traversal detected" )
    }

    return fileRead( cleanPath )
}
```

---

## Input Validation

```boxlang
// Validate and sanitize input at the boundary
function processContactForm( required struct form ) {
    var errors = []

    // Length limits
    if ( form.name.len() > 100 ) errors.append( "Name too long" )

    // Email format
    if ( !isValid( "email", form.email ) ) errors.append( "Invalid email" )

    // Numeric ranges
    if ( !isNumeric( form.age ) || form.age < 0 || form.age > 150 ) {
        errors.append( "Invalid age" )
    }

    // Allowlist for enum-style fields
    var validTopics = [ "support", "sales", "billing" ]
    if ( !validTopics.contains( lCase( form.topic ) ) ) {
        errors.append( "Invalid topic" )
    }

    if ( !errors.isEmpty() ) {
        throw( type="ValidationException", message=errors.toList( ", " ) )
    }
}
```

---

## Remote Function Exposure (Web Services)

When exposing class functions as web-accessible endpoints, explicitly control access:

```boxlang
class {
    remote struct function getUser( required numeric id ) returnformat="json" {
        // Always authenticate remote calls
        if ( !isAuthenticated() ) {
            httpSetResponseStatus( 401 )
            return { error: "Unauthorized" }
        }

        // Validate and sanitize the id parameter
        if ( !isValid( "integer", arguments.id ) || arguments.id < 1 ) {
            httpSetResponseStatus( 400 )
            return { error: "Invalid user ID" }
        }

        return userService.getById( arguments.id )
    }
}
```

---

## Production Hardening Checklist

- [ ] Set `populateServerSystemScope: false` unless needed
- [ ] Configure `disallowedBifs` to remove dangerous BIFs (`systemExecute`, `createObject` if not needed)
- [ ] Configure `disallowedImports` for Java class access restrictions
- [ ] Set `disallowedComponents: ["execute"]` to prevent OS command execution
- [ ] All secrets via environment variables — none in code or committed config
- [ ] All SQL via parameterized queries (`queryParam`)
- [ ] All user output HTML-encoded with context-appropriate encoding functions
- [ ] CSRF tokens on all state-changing forms
- [ ] File uploads stored outside webroot, validated by extension and size
- [ ] Session timeouts configured; session rotated on privilege elevation
- [ ] Enable `trustedCache: true` and `classResolverCache: true` in production
- [ ] HTTPS enforced at the reverse proxy or container level
- [ ] Dependency modules kept up to date (check `box outdated`)