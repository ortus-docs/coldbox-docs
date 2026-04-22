---
name: boxlang-web-development
description: "Use this skill when building BoxLang web applications: Application.bx lifecycle, request/response handling, sessions, forms, REST APIs, HTTP clients, routing, CSRF protection, Server-Sent Events, or configuring CommandBox/MiniServer."
---

# BoxLang Web Development

## Overview

BoxLang web applications revolve around `Application.bx`, which acts as the
application lifecycle controller. The runtime handles HTTP requests through a
well-defined lifecycle, with full support for REST, sessions, CSRF, SSE, and
both MiniServer (dev) and CommandBox (production) deployment.

For a deep dive into multi-app isolation, descriptor discovery, and advanced
`Application.bx` patterns, see the
[`application-descriptor`](../application-descriptor/SKILL.md) skill.

## Application.bx

`Application.bx` lives at the web root and configures the entire application:

```boxlang
// Application.bx
class {

    // Application settings
    this.name            = "MyApp"
    this.sessionManagement = true
    this.sessionTimeout  = createTimeSpan( 0, 2, 0, 0 )  // 2 hours
    this.applicationTimeout = createTimeSpan( 1, 0, 0, 0 )  // 1 day
    this.setClientCookies = true

    // Java classpath additions
    this.javaSettings = {
        loadPaths: [ "lib/" ],
        loadColdFusionClassPath: false,
        reloadOnChange: false
    }

    // Datasource defaults
    this.datasource = "mainDB"

    // --- Lifecycle Events ---

    // Fires once when the application first starts
    boolean function onApplicationStart() {
        application.version = "1.0.0"
        application.config  = loadConfig()
        return true
    }

    // Fires before every request
    boolean function onRequestStart( required string targetPage ) {
        // Auth checks, request logging, CORS headers
        if ( !isAuthenticated() && !isPublicRoute( arguments.targetPage ) ) {
            location( url="/login", addToken=false )
        }
        return true
    }

    // Fires after every request
    void function onRequestEnd( required string targetPage ) {
        // Cleanup, audit logging
    }

    // Called when request target is a .bx file (optional — allows custom routing)
    void function onRequest( required string targetPage ) {
        include arguments.targetPage
    }

    // Fires when a new session starts
    boolean function onSessionStart() {
        session.cart    = []
        session.userId  = ""
        session.isLoggedIn = false
        return true
    }

    // Fires when a session ends
    void function onSessionEnd( required struct sessionScope, required struct appScope ) {
        auditService.recordLogout( arguments.sessionScope.userId )
    }

    // Global error handler
    boolean function onError( required any exception, required string eventName ) {
        errorService.log( arguments.exception )
        include "views/error.bxm"
        return true
    }

    // Missing template handler
    boolean function onMissingTemplate( required string targetPage ) {
        location( url="/404" )
        return true
    }

}
```

## Request Scope and CGI Variables

```boxlang
// URL variables (?key=value)
var page = url.page ?: 1
var query = url.q ?: ""

// Form variables (POST body)
var username = form.username ?: ""
var password = form.password ?: ""

// CGI scope
var userAgent   = cgi.http_user_agent
var requestMethod = cgi.request_method  // GET, POST, PUT, DELETE
var remoteAddr  = cgi.remote_addr
var serverName  = cgi.server_name

// Request scope (per-request shared storage)
request.startTime = getTickCount()
request.userId    = session.userId
```

## Handling Forms

```boxlang
// Process a form submission
if ( cgi.request_method == "POST" ) {
    // Validate
    if ( !len( trim( form.email ) ) ) {
        request.errors.append( "Email is required" )
    }

    if ( !request.errors.len() ) {
        userService.create({
            email    : form.email,
            username : form.username,
            password : hashPassword( form.password )
        })
        location( url="/dashboard", addToken=false )
    }
}
```

## REST API Patterns

```boxlang
// REST endpoint using onRequest routing in Application.bx
void function onRequest( required string targetPage ) {
    var method  = cgi.request_method
    var path    = cgi.path_info
    var router  = new Router()

    router.get( "/api/users",      "handlers.UsersHandler", "index" )
    router.post( "/api/users",     "handlers.UsersHandler", "create" )
    router.get( "/api/users/:id",  "handlers.UsersHandler", "show" )
    router.put( "/api/users/:id",  "handlers.UsersHandler", "update" )
    router.delete( "/api/users/:id", "handlers.UsersHandler", "delete" )

    router.dispatch( method, path )
}

// Handler: handlers/UsersHandler.bx
class {

    function index() {
        var users = userService.findAll()
        renderJSON( users )
    }

    function show( required numeric id ) {
        var user = userService.findById( arguments.id )
        if ( isNull( user ) ) {
            httpStatus( 404 )
            renderJSON({ error: "Not found" })
            return
        }
        renderJSON( user )
    }

    function create() {
        var data = deserializeJSON( getHTTPRequestData().content )
        var user = userService.create( data )
        httpStatus( 201 )
        renderJSON( user )
    }

}

// Helper functions
function renderJSON( required any data ) {
    cfheader( name="Content-Type", value="application/json" )
    writeOutput( serializeJSON( arguments.data ) )
    abort
}

function httpStatus( required numeric code ) {
    cfheader( statusCode=arguments.code )
}
```

## HTTP Client

```boxlang
// Simple GET
var response = httpGet( "https://api.example.com/data" )
var data     = deserializeJSON( response.fileContent )

// Full bx:http request
bx:http url="https://api.example.com/users" method="POST" result="apiResponse" {
    bx:httpparam type="header" name="Authorization" value="Bearer #token#"
    bx:httpparam type="header" name="Content-Type"  value="application/json"
    bx:httpparam type="body"   value=serializeJSON({ name: "Ada", email: "ada@example.com" })
}

var response = deserializeJSON( apiResponse.fileContent )

// HTTP with query string
bx:http url="https://api.example.com/search" method="GET" result="searchResult" {
    bx:httpparam type="url" name="q"     value="boxlang"
    bx:httpparam type="url" name="limit" value="10"
}

// File download
bx:http url="https://example.com/report.pdf" method="GET" result="pdfResponse"
        getAsBinary="yes"
fileWrite( expandPath( "./downloads/report.pdf" ), pdfResponse.fileContent )
```

## Session Management

```boxlang
// Set session values
session.userId    = user.getId()
session.username  = user.getUsername()
session.isLoggedIn = true
session.cart       = []

// Check session
if ( session.isLoggedIn ?: false ) {
    // Authenticated
}

// Invalidate session on logout
sessionInvalidate()
location( url="/login", addToken=false )

// Extend session programmatically
sessionRotate()
```

## CSRF Protection

```boxlang
// Generate CSRF token (stores in session)
var token = csrfGenerateToken( "loginForm" )

// In your form (bxm template):
// <input type="hidden" name="csrfToken" value="#csrfGenerateToken('loginForm')#">

// Validate on POST
if ( !csrfVerifyToken( form.csrfToken, "loginForm" ) ) {
    httpStatus( 403 )
    writeOutput( "Invalid CSRF token" )
    abort
}
```

## Server-Sent Events (SSE)

```boxlang
// SSE endpoint: sse/updates.bxs
cfheader( name="Content-Type",      value="text/event-stream" )
cfheader( name="Cache-Control",     value="no-cache" )
cfheader( name="X-Accel-Buffering", value="no" )

var i = 0
while ( i < 100 ) {
    var data = getLatestUpdates()
    writeOutput( "data: #serializeJSON(data)##chr(10)##chr(10)#" )
    flush
    sleep( 1000 )
    i++
}
```

## Output and Rendering

```boxlang
// Include a template
include "views/header.bxm"
include "views/user/profile.bxm"

// Save output to variable
var rendered = ""
savecontent variable="rendered" {
    include "views/email/welcome.bxm"
}

// Set response headers
cfheader( name="X-Custom-Header", value="myValue" )
cfcontent( type="application/pdf" )

// Redirect
location( url="/dashboard", addToken=false )
location( url="/login", statusCode=302 )
```

## MiniServer Configuration (Development)

`miniserver.json` in the project root:

```json
{
    "host": "localhost",
    "port": 8080,
    "webroot": "./www",
    "debug": true,
    "warmUpURLs": ["/"],
    "rewrites": {
        "enable": true,
        "config": "urlrewrite.xml"
    }
}
```

Start MiniServer:
```bash
boxlang-miniserver
# or via CommandBox
box server start
```

## CommandBox Server Configuration (Production)

`server.json`:

```json
{
    "name": "MyApp",
    "web": {
        "http": { "port": 8080 },
        "https": { "port": 8443, "enable": true },
        "webroot": "www"
    },
    "app": {
        "cfengine": "boxlang",
        "javaVersion": "openjdk21_jdk"
    },
    "jvm": {
        "heapSize": "512m",
        "maxHeapSize": "1024m",
        "args": "-Duser.timezone=UTC"
    },
    "env": {
        "DB_HOST": "localhost",
        "DB_NAME": "myapp"
    }
}
```

## SOAP Web Services

BoxLang 1.8.0+ provides the `soap()` BIF for consuming SOAP 1.1/1.2 web services.
It automatically parses WSDL documents and converts SOAP XML responses to BoxLang
native types.

### Basic SOAP Client

```boxlang
// Create client from WSDL URL
var ws = soap( "http://example.com/service.wsdl" )

// Invoke an operation with named parameters
var result = ws.invoke( "getCustomer", { customerId: 12345 } )
writeOutput( "Name: #result.customerName#" )
```

### Client with Authentication and Timeout

```boxlang
var ws = soap( "http://example.com/service.wsdl" )
    .withBasicAuth( "apiUser", "secret" )
    .timeout( 30 )

var customer = ws.invoke( "getCustomer", { customerId: 42 } )
```

### Custom Headers

```boxlang
var ws = soap( "http://secure.example.com/service.wsdl" )
    .header( "X-API-Key", "abc123" )
    .header( "X-Tenant-ID", "tenant001" )
    .timeout( 45 )

var result = ws.invoke( "getData" )
```

### Service Inspection (Discover Operations)

```boxlang
var ws = soap( "http://example.com/service.wsdl" )

// List all available operations
var operations = ws.getOperations()
operations.each( ( op ) -> writeOutput( op & "<br>" ) )

// Get input parameter details for an operation
var info = ws.getOperationInfo( "createOrder" )
info.inputParameters.each( ( param ) -> {
    writeOutput( "#param.name# (#param.type#)<br>" )
})
```

### Error Handling (SOAP Faults → BoxLang Exceptions)

```boxlang
try {
    var ws = soap( "http://example.com/service.wsdl" )
    var result = ws.invoke( "processOrder", { orderId: orderId } )
} catch ( "soap.Fault" e ) {
    writeOutput( "SOAP fault: #e.message#" )
} catch ( "soap.ConnectionError" e ) {
    writeOutput( "Connection failed: #e.message#" )
} catch ( any e ) {
    writeOutput( "Unexpected error: #e.message#" )
}
```

**When to use `soap()` vs `bx:http`:**

| Use `soap()`       | Use `bx:http` / `httpGet()` |
|--------------------|------------------------------|
| Enterprise/legacy SOAP services | Modern REST/JSON APIs |
| WSDL-defined contracts | Lightweight, fast communication |
| WS-Security required | JSON preferred |
| Complex type mapping | Simple HTTP calls |

## References

- [Application.bx](https://boxlang.ortusbooks.com/boxlang-framework/applications)
- [HTTP Module](https://boxlang.ortusbooks.com/boxlang-framework/http)
- [Sessions](https://boxlang.ortusbooks.com/boxlang-framework/sessions)
- [CommandBox](https://boxlang.ortusbooks.com/getting-started/running-boxlang/commandbox)
- [MiniServer](https://boxlang.ortusbooks.com/getting-started/running-boxlang/miniserver)