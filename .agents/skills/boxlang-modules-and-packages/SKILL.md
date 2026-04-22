---
name: boxlang-modules-and-packages
description: "Use this skill when installing, configuring, or using BoxLang modules: box install, boxlang.json module settings, BoxLang+ premium modules (bx-pdf, bx-redis, bx-csv, bx-spreadsheet), CFML compatibility, ORM, mail, and module introspection."
---

# BoxLang Modules and Packages

## Overview

BoxLang's module system allows the runtime to be extended with additional built-in
functions (BIFs), components, and services. Modules are installed via CommandBox's
`box` CLI and configured in `boxlang.json` or `Application.bx`. Many modules
auto-register their BIFs globally — no imports required after installation.

## Installing Modules

```bash
# Install via CommandBox
box install bx-redis
box install bx-pdf
box install bx-csv
box install bx-spreadsheet

# Install a specific version
box install bx-redis@2.1.0

# Install multiple at once
box install bx-redis bx-csv bx-mail

# List installed modules
box list
```

## Configuring Modules in `boxlang.json`

```json
{
    "modules": {
        "bx-redis": {
            "enabled": true,
            "settings": {
                "host": "${REDIS_HOST:localhost}",
                "port": 6379,
                "password": "${REDIS_PASSWORD:}"
            }
        },
        "bx-mail": {
            "enabled": true,
            "settings": {
                "spooling": true,
                "serverInterval": 5000,
                "smtp": {
                    "host": "${SMTP_HOST}",
                    "port": 587,
                    "tls": true,
                    "auth": true,
                    "username": "${SMTP_USER}",
                    "password": "${SMTP_PASS}"
                }
            }
        },
        "compat-cfml": {
            "enabled": true,
            "settings": {
                "engine": "Lucee"
            }
        }
    }
}
```

## Disabling a Module

```json
{
    "modules": {
        "bx-orm": {
            "enabled": false
        }
    }
}
```

---

## Core Free Modules

### `bx-compat-cfml` — CFML Compatibility

Enables CFML syntax parsing and CFML-specific functions/components for migrating
legacy ColdFusion or Lucee applications:

```json
{
    "modules": {
        "compat-cfml": {
            "enabled": true,
            "settings": { "engine": "Lucee" }
        }
    }
}
```

Once enabled, `.cfm`, `.cfc`, and CFML syntax are recognized alongside BoxLang syntax.

### `bx-web-support` — Web Server Integration

Provides web request/response handling, session management, and CGI scope support.
Required for any web application. Usually auto-loaded by CommandBox/MiniServer.

### `bx-mail` — Email

```boxlang
// Send an email
bx:mail
    to="user@example.com"
    from="noreply@myapp.com"
    subject="Welcome to MyApp!"
    type="html" {

    writeOutput( "<h1>Welcome, #name#!</h1>" )
    writeOutput( "<p>Your account is ready.</p>" )
}

// Plain text with attachment
bx:mail to="admin@myapp.com" from="app@myapp.com" subject="Report" {
    bx:mailpart type="text" {
        writeOutput( "See attached report." )
    }
    bx:mailparam file=expandPath("./reports/daily.pdf") disposition="attachment"
}
```

### `bx-orm` — Object Relational Mapping (Hibernate)

```boxlang
// In Application.bx
class {
    this.ormEnabled = true
    this.ormSettings = {
        datasource      : "mainDB",
        dbcreate        : "update",
        flushAtRequestEnd : true,
        autoManageSession : true
    }
}

// Persistent entity: models/User.bx
class persistent="true" table="users" accessors="true" {
    property name="id"    column="id"    fieldtype="id" generator="native"
    property name="name"  column="name"  type="string"
    property name="email" column="email" type="string"

    // Relationship
    property name="orders" fieldtype="one-to-many" cfc="models.Order"
              cascade="all" lazy="true"
}

// ORM operations
var user = entityLoad( "User", userId )
entitySave( new models.User( name="Ada", email="ada@example.com" ) )
entityDelete( user )

// HQL queries
var users = ORMExecuteQuery( "FROM User WHERE status = :status", { status: "active" } )
```

---

## BoxLang+ Premium Modules

BoxLang+ modules require a subscription at [boxlang.io](https://boxlang.io).

### `bx-pdf` — PDF Generation

```boxlang
// Generate PDF from HTML
bx:pdf action="write" filename=expandPath("./output/report.pdf") overwrite="true" {
    bx:pdfparam type="header" {
        writeOutput( "<h3>Monthly Report</h3>" )
    }
    writeOutput( "<h1>Sales Summary</h1>" )
    writeOutput( "<p>Total: $#totalSales#</p>" )
    include "views/sales-table.bxm"
}

// Merge PDFs
bx:pdf action="merge" destination=expandPath("./merged.pdf") overwrite="true" {
    bx:pdfparam source=expandPath("./cover.pdf")
    bx:pdfparam source=expandPath("./report.pdf")
}
```

### `bx-csv` — CSV Processing

```boxlang
// Parse CSV
var data = csvParse( fileRead("data.csv") )

// Parse with options
var data = csvParse(
    fileRead( "data.csv" ),
    {
        hasHeaders   : true,
        delimiter    : ",",
        quoteChar    : '"',
        charset      : "UTF-8"
    }
)

// Iterate rows
for ( var row in data ) {
    processRow( row )
}

// Generate CSV
var output = csvSerialize(
    queryExecute( "SELECT id, name, email FROM users" ),
    { hasHeaders: true, delimiter: "," }
)
fileWrite( "export.csv", output )
```

### `bx-spreadsheet` — Excel (XLSX)

```boxlang
// Read a spreadsheet
var wb = spreadsheetRead( expandPath("./data.xlsx") )
var sheet = spreadsheetGetSheetName( wb, 1 )
var data  = spreadsheetRead( expandPath("./data.xlsx"), true, true )

// Create a spreadsheet
var wb = spreadsheetNew( "Report" )
spreadsheetAddRow( wb, ["Name", "Email", "Revenue"] )

for ( var user in users ) {
    spreadsheetAddRow( wb, [user.name, user.email, user.revenue] )
}

// Format header row
spreadsheetFormatRow( wb, {
    bold       : true,
    bgcolor    : "##336699",
    fontcolor  : "##FFFFFF"
}, 1 )

spreadsheetWrite( wb, expandPath("./report.xlsx") )
```

### `bx-redis` — Redis Integration

```boxlang
// After configuring the module, use Redis as a cache provider
// (see caching skill for full details)

// Direct Redis operations via the Redis client
var redis = getBoxService( "RedisService" )
redis.set( "key", "value", 3600 )  // with TTL in seconds
var val = redis.get( "key" )
redis.del( "key" )
redis.incr( "counter" )
redis.lpush( "queue", serializeJSON(job) )
var job = redis.rpop( "queue" )
```

### `bx-ldap` — LDAP Directory

```boxlang
bx:ldap
    server="ldap.example.com"
    port="389"
    username="cn=admin,dc=example,dc=com"
    password="#ldapPassword#"
    action="query"
    name="result"
    start="dc=example,dc=com"
    attributes="cn,mail,sn"
    filter="(objectClass=person)"

for ( var person in result ) {
    writeOutput( "#person.cn# — #person.mail#<br>" )
}
```

### `bx-image` — Image Manipulation

```boxlang
// Read and resize
var img = imageRead( expandPath("./upload/photo.jpg") )
imageResize( img, 800, 600 )
imageWrite( img, expandPath("./output/photo-resized.jpg") )

// Crop
imageCrop( img, x=0, y=0, width=400, height=300 )

// Add watermark
var watermark = imageNew( "", 200, 50, "argb", "##00000000" )
imageDrawText( watermark, "© 2024 MyApp", 10, 30, { size: 16, color: "##FFFFFF" } )
imagePaste( img, watermark, 600, 550 )

// Get info
var info = imageInfo( img )
writeOutput( "Width: #info.width#, Height: #info.height#" )
```

---

## Module Introspection

```boxlang
// List all loaded modules
var modules = getBoxService( "ModuleService" ).getLoadedModules()
modules.each( (name) -> writeOutput( name & "<br>" ) )

// Get module info
var info = getModuleInfo( "bx-redis" )
writeOutput( "Version: #info.version#" )
writeOutput( "Author: #info.author#" )

// Check if a module is loaded
if ( isModuleLoaded( "bx-redis" ) ) {
    // use Redis
}
```

## References

- [Modularity](https://boxlang.ortusbooks.com/boxlang-framework/modularity)
- [Module Configuration](https://boxlang.ortusbooks.com/getting-started/configuration#modules)
- [BoxLang+ Modules](https://boxlang.ortusbooks.com/boxlang-framework/boxlang-plus)
- [ForgeBox Package Manager](https://forgebox.io)
- [bx-pdf](https://boxlang.ortusbooks.com/boxlang-framework/boxlang-plus/modules/bx-pdf)
- [bx-csv](https://boxlang.ortusbooks.com/boxlang-framework/boxlang-plus/modules/bx-csv)
- [bx-spreadsheet](https://boxlang.ortusbooks.com/boxlang-framework/boxlang-plus/modules/bx-spreadsheet)