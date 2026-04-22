---
title: ColdBox Framework Core Guidelines
description: ColdBox HMVC framework conventions, structure, handlers, models, views, REST APIs, testing, and integrations
---

# ColdBox Framework Core Guidelines

## Overview

ColdBox is a conventions-based HMVC (Hierarchical Model-View-Controller) framework for CFML and BoxLang applications. It provides a complete ecosystem for building modern, scalable web applications and REST APIs.

## Event Handlers (Controllers)

### Handler Conventions

- Extend `coldbox.system.EventHandler`
- Located in `/handlers/` directory
- Use plural nouns: `Users.cfc`, `Orders.cfc`, `Products.cfc`
- Actions are public functions receiving `event`, `rc`, `prc`

### Basic Handler

```boxlang
class Users extends coldbox.system.EventHandler {
    property name="userService" inject;
    property name="log" inject="logbox:logger:{this}";

    function index( event, rc, prc ) {
        prc.users = userService.getAll()
        event.setView( "users/index" )
    }

    function show( event, rc, prc ) {
        prc.user = userService.getById( rc.id ?: 0 )
        event.setView( "users/show" )
    }

    function create( event, rc, prc ) {
        var user = userService.create( rc )
        flash.put( "notice", "User created successfully" )
        relocate( "users.show", { id: user.id } )
    }
}
```

### RESTful Handler

RESTful handlers use `event.renderData()` to return structured responses. Actions map to HTTP verbs
(index=GET, create=POST, update=PUT, delete=DELETE). See `.ai/skills/coldbox-rest-api-development/SKILL.md`
for full implementation patterns.

## Request Context (Event Object)

The `event` object is your gateway to request data and framework features.

### Getting/Setting Values

```boxlang
// Get from RC (request collection - URL/FORM merged)
var userId = event.getValue( "userId", 0 )
var email = event.getTrimValue( "email", "" )

// Set in PRC (private request collection - safe, internal)
event.setValue( "userName", user.name )
event.setPrivateValue( "internalData", sensitiveData )

// Param a value (set default if not exists)
event.paramValue( "page", 1 )
event.paramValue( "perPage", 25 )

// Get entire collections
var rc = event.getCollection()
var prc = event.getPrivateCollection()
```

### Request Metadata

```boxlang
// Current execution info
var handler = event.getCurrentHandler()      // "users"
var action = event.getCurrentAction()        // "index"
var eventName = event.getCurrentEvent()      // "users.index"
var module = event.getCurrentModule()        // "admin" (if in module)

// View/Layout info
var view = event.getCurrentView()
var layout = event.getCurrentLayout()

// Routing info
var route = event.getCurrentRoute()
var routeName = event.getCurrentRouteName()
```

### Rendering

```boxlang
// Set view to render
event.setView( "users/index" )
event.setView( view="users/show", layout="custom" )

// Set layout only
event.setLayout( "admin" )

// Render data (JSON/XML/PDF/etc)
event.renderData(
    data = users,
    type = "json",
    statusCode = 200
)

// Prevent rendering
event.noRender()

// Render nothing (204 response)
event.noExecution()
```

### Navigation

```boxlang
// Relocate to another event
relocate( "users.index" )
relocate( event="users.show", queryString="id=5" )

// Build links
var url = event.buildLink( "users.show" )
var url = event.buildLink( to="users.edit", queryString="id=#user.id#" )
var url = event.buildLink( to="api.users.show", ssl=true )
```

### HTTP Operations

```boxlang
// Get HTTP method
var method = event.getHTTPMethod()  // GET, POST, PUT, DELETE

// Check HTTP method
if ( event.isGET() ) { }
if ( event.isPOST() ) { }
if ( event.isPUT() ) { }
if ( event.isDELETE() ) { }

// Request type
if ( event.isAjax() ) { }
if ( event.isSSL() ) { }

// Set HTTP headers
event.setHTTPHeader( name="X-Custom-Header", value="value" )
event.setHTTPHeader( statusCode=404, statusText="Not Found" )
```

## Dependency Injection (WireBox)

### Property Injection

```boxlang
class Users extends coldbox.system.EventHandler {
    // Auto-inject by name convention
    property name="userService" inject;

    // Inject from specific path
    property name="utils" inject="models.Utils";

    // Inject by ID
    property name="mailService" inject="id:MailService";

    // Inject using DSL
    property name="cache" inject="cachebox:default";
    property name="log" inject="logbox:logger:{this}";
    property name="settings" inject="coldbox:setting:mySettings";
    property name="wirebox" inject="wirebox";
}
```

### getInstance() Method

```boxlang
// Get instances programmatically
var userService = getInstance( "UserService" )
var cache = getInstance( "cachebox:default" )
var settings = getInstance( "coldbox:setting:appName" )
```

## Routing

### Route Configuration

Located in `config/Router.cfc`:

```boxlang
function configure() {
    // Enable full rewrites
    setFullRewrites( true )

    // Basic route
    route( "/" ).to( "main.index" )
    route( "/about" ).to( "main.about" )

    // Route with placeholders
    route( "/blog/:year/:month/:day/:slug" ).to( "blog.show" )

    // Optional placeholders
    route( "/search/:term?/:page?" ).to( "search.results" )

    // Constrained placeholders
    route( "/user/:id-numeric" ).to( "users.show" )
    route( "/blog/:year-regex:(\\d{4})" ).to( "blog.archive" )

    // Named routes
    route( "/contact" )
        .as( "contactPage" )
        .to( "main.contact" )

    // RESTful resources
    resources( "users" )
    // Creates: index, create, show, update, delete routes

    // API routes
    group( { pattern="/api/v1", handler="api" }, () => {
        route( "/users" ).to( "users.index" )
        route( "/users/:id" ).to( "users.show" )
    } )

    // Route to view directly
    route( "/terms" ).toView( "legal/terms" )

    // Route to response function
    route( "/health" ).toResponse( ( event, rc, prc ) => {
        return { status: "ok", timestamp: now() }
    } )

    // Redirect routes
    route( "/old-page" ).toRedirect( "/new-page", 301 )
}
```

### Module Routing

```boxlang
// In module's config/Router.cfc
function configure() {
    route( "/" ).to( "home.index" )
    route( "/products" ).to( "products.list" )
}

// Access: /mymodule/products
// Or with custom entrypoint: /shop/products
```

## Interceptors (AOP)

Interceptors provide aspect-oriented programming for cross-cutting concerns.

### Built-in Interception Points

```boxlang
// Application lifecycle
afterConfigurationLoad
afterAspectsLoad
afterCacheStartup
onException
onRequestCapture
preProcess
preEvent
postEvent
postProcess
preLayout
postLayout
preRender
postRender

// Module lifecycle
preModuleLoad
postModuleLoad
preModuleUnload
postModuleUnload
```

### Creating Interceptors

Interceptors extend `coldbox.system.Interceptor` and implement listener methods matching the interception
point names above. Access the event via the `event` argument and shared data via `interceptData`.
See `.ai/skills/coldbox-interceptor-development/SKILL.md` for full implementation patterns.

### Registering Interceptors

In `config/ColdBox.cfc`:

```boxlang
interceptors = [
    { class="interceptors.SecurityInterceptor" },
    {
        class="interceptors.RequestLogger",
        properties={ logPath="/logs/requests" }
    }
]
```

### Announcing Custom Events

```boxlang
// In handlers or models
announceInterception( "onUserLogin", { user: user } )
announceInterception( "onOrderComplete", { order: order, total: total } )

// In interceptors - listen for custom events
function onUserLogin( event, interceptData ) {
    var user = interceptData.user
    log.info( "User logged in: #user.email#" )
}
```

## Modules

Modules are self-contained sub-applications that can be plugged into any ColdBox application.

### Module Structure

```
/modules/shop/
    ModuleConfig.cfc
    /handlers
    /models
    /views
    /layouts
    /interceptors
    config/Router.cfc
```

### Module Configuration

`ModuleConfig.cfc` declares `this.title`, `this.author`, `this.version`, `this.entryPoint`, and a
`configure()` function that sets `settings`, `interceptors`, `wirebox` mappings, etc.
See `.ai/skills/coldbox-module-development/SKILL.md` for full implementation patterns.

## Configuration (config/ColdBox.cfc)

```boxlang
component {
    function configure() {
        coldbox = {
            appName = "My Application",
            reinitPassword = "",
            handlersIndexAutoReload = true,  // Dev only
            handlerCaching = false,          // Dev only
            viewCaching = false,             // Dev only
            eventCaching = false,            // Dev only
            defaultEvent = "main.index",
            requestStartHandler = "main.onRequestStart",
            requestEndHandler = "main.onRequestEnd",
            applicationStartHandler = "main.onAppInit",
            onInvalidEvent = "main.notFound",
            customErrorTemplate = "/views/main/error.cfm"
        }

        settings = {
            mySettings = "value",
            apiKey = getSystemSetting( "API_KEY", "" )
        }

        interceptors = [
            { class="interceptors.Security" }
        ]

        moduleSettings = {
            cbdebugger = {
                enabled = true
            }
        }
    }
}
```

## Flash Scope

Persist data across redirects:

```boxlang
// Put data in flash
flash.put( "notice", "User created successfully" )
flash.put( "user", user )

// Get from flash
var notice = flash.get( "notice", "" )
var user = flash.get( "user" )

// Keep flash for next request
flash.keep( "userData" )

// Discard flash
flash.discard( "tempData" )
```

## Best Practices

- **Use RESTful naming** - Handlers are plural nouns, actions are standard REST verbs
- **Leverage dependency injection** - Use `property inject` instead of manual creation
- **Use PRC for internal data** - Keep RC for user input only
- **Create service layers** - Keep handlers thin, move logic to services
- **Use interceptors for cross-cutting concerns** - Security, logging, caching
- **Build in modules** - Organize large applications into modules
- **Use named routes** - Makes refactoring easier with `buildLink( name="routeName" )`
- **Cache aggressively** - Use CacheBox for expensive operations
- **Log appropriately** - Use LogBox with proper severity levels
- **Test everything** - Use TestBox for unit and integration tests

## Documentation

For complete ColdBox documentation, modules, and advanced features, consult the ColdBox MCP server or visit:
https://coldbox.ortusbooks.com

---

> **Implementation patterns are in skills.** Load the relevant skill with `read_file` when implementing:
> - Handler CRUD: `.ai/skills/coldbox-handler-development/SKILL.md`
> - REST APIs: `.ai/skills/coldbox-rest-api-development/SKILL.md`
> - Routing config: `.ai/skills/coldbox-routing-development/SKILL.md`
> - Interceptors: `.ai/skills/coldbox-interceptor-development/SKILL.md`
> - Modules: `.ai/skills/coldbox-module-development/SKILL.md`
> - Testing: `.ai/skills/coldbox-testing-handler/SKILL.md`
