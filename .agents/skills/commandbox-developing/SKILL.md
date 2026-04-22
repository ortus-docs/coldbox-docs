---
name: commandbox-developing
description: "Use this skill for developing CommandBox extensions: creating custom commands (CFCs with run() method), command namespaces, parameters and tab completion, WireBox DI injection, command output with print helper, running sub-commands, creating modules (ModuleConfig.cfc), module conventions (commands/models/interceptors), interceptors, lifecycle events, custom interception points, injection DSL, user settings, and linking modules for development."
---

# Developing for CommandBox

## Overview

CommandBox is fully extensible via CFML modules. You can create custom commands, interceptors, and models — all packaged as reusable modules. Modules live in `~/.CommandBox/cfml/modules/` for personal use or are distributed via ForgeBox.

```bash
# Reload shell after code changes
reload    # alias: r

# Enable developer mode (auto-reload before each command — slow but convenient)
config set developerMode=true
config set developerMode=false
```

---

## Custom Commands

### Minimal Command

```bash
# Create module structure
mkdir -p ~/.CommandBox/cfml/modules/myModule/commands
```

**`~/.CommandBox/cfml/modules/myModule/ModuleConfig.cfc`**

```javascript
component {
    function configure() {}
}
```

**`~/.CommandBox/cfml/modules/myModule/commands/Hello.cfc`**

```javascript
/**
 * Say hello to someone
 *
 * {code:bash}
 * hello Brad
 * hello --name=Brad
 * {code}
 */
component {

    /**
     * @name.hint The name to greet
     * @name.optionsUDF suggestNames
     */
    function run( required string name ) {
        print.boldGreenLine( "Hello, #name#!" );
    }

    /**
     * Tab completion for name parameter
     */
    array function suggestNames( string paramSoFar, struct passedNamedParameters ) {
        return [ "Alice", "Bob", "Charlie" ];
    }

}
```

```bash
# Use the command
reload
hello Brad
hello --name=Brad
```

---

### Namespaced Commands

Create subfolders under `commands/` to create namespaced commands:

```
commands/
├── greet/
│   ├── Hello.cfc    → box greet hello
│   └── Goodbye.cfc  → box greet goodbye
└── deploy/
    ├── Staging.cfc  → box deploy staging
    └── Prod.cfc     → box deploy prod
```

---

### Command Parameters

```javascript
/**
 * @param1.hint Required string parameter
 * @param2.hint Optional parameter with default
 * @param3.hint Boolean flag
 * @param4.hint Parameter with enum options
 * @param4.options small,medium,large
 * @param5.hint File path (auto-completes filesystem)
 * @param5.optionsFileComplete true
 * @param6.hint Directory (auto-completes directories)
 * @param6.optionsDirectoryComplete true
 */
function run(
    required string param1,
    string param2 = "default",
    boolean param3 = false,
    string param4 = "small",
    string param5,
    string param6
) {
    // Named param
    print.line( arguments.param1 );

    // Flag usage: myCommand --param3 or myCommand --noParam3
}
```

---

### WireBox Dependency Injection

All command CFCs are wired via WireBox and have access to services:

```javascript
component {

    // Inject CommandBox services
    property name="artifactService"   inject="artifactService";
    property name="packageService"    inject="packageService";
    property name="serverService"     inject="serverService";
    property name="configService"     inject="configService";
    property name="forgeBox"          inject="ForgeBox";

    // Inject models from other modules
    property name="myModel"           inject="myModel@myModule";

    function run() {
        var artifacts = artifactService.listArtifacts();
        for ( var pkg in artifacts ) {
            print.boldCyanLine( pkg );
        }
    }

}
```

Also available: `getInstance()` and `variables.wirebox`:

```javascript
var svc = getInstance( "artifactService" );
var myObj = wirebox.getInstance( "myModel@myModule" );
```

---

### Print Helper (Output)

```javascript
function run() {
    // Basic output
    print.line( "Normal text" );
    print.line();        // blank line

    // Colors (supports 256 colors)
    print.green( "inline green" );
    print.greenLine( "green + newline" );
    print.redLine( "error" );
    print.yellowLine( "warning" );
    print.cyanLine( "info" );
    print.blueLine( "debug" );
    print.boldLine( "bold" );
    print.boldGreenLine( "bold green" );
    print.MistyRose3Line( "fancy 256-color name" );

    // Background color: "on" prefix
    print.onBlackWhiteLine( "white text on black bg" );

    // Indentation
    print.indentedLine( "  indented text" );

    // Underline
    print.underline( "underlined text" );

    // Tables
    print.table(
        headers = [ "Package", "Version" ],
        data    = [ [ "coldbox", "7.0.0" ], [ "testbox", "5.0.0" ] ]
    );

    // Trees
    print.tree( [
        { label: "root", children: [
            { label: "child1" },
            { label: "child2" }
        ]}
    ] );

    // Return value (simplest output)
    return "Hello World!";
}
```

---

### Running Other Commands

```javascript
function run() {
    // Run a command
    command( "install coldbox" ).run();

    // Capture output
    var version = command( "package show version" ).run( returnOutput=true );

    // With params struct
    command( "server start" )
        .params( port=8080, openBrowser=false )
        .run();

    // Flags
    command( "install coldbox" )
        .flag( "verbose" )
        .flag( "noSave" )
        .run();

    // Get exit code
    var exitCode = command( "testbox run" ).run( returnExitCode=true );
    if ( exitCode != 0 ) {
        error( "Tests failed!" );
    }
}
```

---

### Error Handling

```javascript
function run() {
    try {
        command( "server start" ).run();
    } catch ( commandException e ) {
        print.redLine( "Command failed: #e.message#" );
        // Set failing exit code
        setExitCode( 1 );
        return;
    }

    // Throw an error to stop execution
    error( "Something went wrong" );
}
```

---

### Interactivity

```javascript
function run() {
    // Ask user for input
    var name = ask( "What is your name? " );
    print.greenLine( "Hello, #name#!" );

    // Ask with hidden input (password)
    var pass = ask( message="Password: ", mask="*" );

    // Yes/No confirmation
    if ( confirm( "Are you sure?" ) ) {
        print.greenLine( "Confirmed!" );
    }

    // Multi-choice selection
    var color = multiSelect()
        .setQuestion( "Pick a color:" )
        .setOptions( [ "red", "green", "blue" ] )
        .ask();
}
```

---

## Modules

### Module Structure

```
~/.CommandBox/cfml/modules/
└── myModule/
    ├── ModuleConfig.cfc      # Required
    ├── box.json              # Package descriptor (for publishing)
    ├── commands/             # Custom commands
    │   └── MyCommand.cfc
    ├── models/               # WireBox-managed services
    │   └── MyService.cfc
    ├── interceptors/         # Event interceptors
    │   └── MyInterceptor.cfc
    └── modules/              # Nested dependency modules
```

### ModuleConfig.cfc

```javascript
/**
 * My CommandBox Module
 */
component {

    // Injected by CommandBox
    property name="shell"          inject="shell";
    property name="moduleMapping"  inject="moduleMapping@MyModule";
    property name="modulePath"     inject="modulePath@MyModule";
    property name="wirebox"        inject="wirebox";
    property name="log"            inject="logbox:logger:{this}";

    function configure() {
        // Module settings (overrideable via config set modules.myModule.*)
        settings = {
            apiUrl:   "https://api.example.com",
            timeout:   30,
            verbose:  false
        };

        // Register interceptors
        interceptors = [
            {
                class: "#moduleMapping#.interceptors.MyInterceptor",
                properties: { coolness: "max" }
            }
        ];

        // Register custom interception points
        interceptorSettings = {
            customInterceptionPoints: "onMyEvent,onMyOtherEvent"
        };

        // Map models manually (optional — convention handles /models/ automatically)
        binder.map( "myAlias" ).to( "#moduleMapping#.models.MyService" );
    }

    // Lifecycle — runs after module is loaded
    function onLoad() {
        log.info( "MyModule loaded!" );
    }

    // Lifecycle — runs before module is unloaded
    function onUnLoad() {
        log.info( "MyModule unloaded." );
    }

    // Shortcut: ModuleConfig doubles as an interceptor
    function onCLIStart( interceptData ) {
        if ( interceptData.shellType == "interactive" && settings.verbose ) {
            shell.callCommand( "echo 'MyModule active'" );
        }
    }

}
```

---

### User Settings (Module Config)

```bash
# Override module defaults from CLI
config set modules.myModule.verbose=true
config set modules.myModule.apiUrl=https://api.staging.example.com

# Read settings
config show modules.myModule.verbose
```

In commands, inject via WireBox:

```javascript
property name="settings" inject="commandbox:moduleSettings:myModule";

function run() {
    print.line( settings.apiUrl );
}
```

---

### Linking Modules for Development

```bash
# Instead of copying, link your module source directory
cd /path/to/my-module-source
package link

# Now changes to source are reflected immediately (with reload)
reload
```

---

## Interceptors

### Creating an Interceptor

```javascript
/**
 * My interceptor
 */
component {

    property name="print" inject="PrintBuffer";
    property name="log"   inject="logbox:logger:{this}";

    // Post-command: modify output
    function postCommand( interceptData ) {
        // interceptData.commandString - the command that ran
        // interceptData.results - output of the command (can be modified)
        if ( settings.uppercase ) {
            interceptData.results = ucase( interceptData.results );
        }
    }

    // Pre-command: can cancel execution
    function preCommand( interceptData ) {
        // interceptData.commandInfo - struct of command metadata
        // interceptData.commandReference - the command CFC instance
        log.debug( "Running: #interceptData.commandString#" );
    }

    // Error handler
    function onException( interceptData ) {
        // interceptData.exception - the cfcatch object
        fileAppend(
            expandPath( "~/commandbox-errors.log" ),
            "#now()# - #interceptData.exception.message##chr(10)#"
        );
    }

}
```

### Core Interception Points

| Point | When |
|-------|------|
| `onCLIStart` | Shell starts (interactive or one-off) |
| `onCLIExit` | Shell exits |
| `preCommand` | Before any command runs |
| `postCommand` | After any command runs |
| `onException` | Unhandled exception |
| `preServerStart` | Before server starts |
| `postServerStart` | After server starts |
| `preServerStop` | Before server stops |
| `postServerStop` | After server stops |
| `preInstall` | Before package install |
| `postInstall` | After package install |
| `prePublish` | Before package publish |
| `postPublish` | After package publish |
| `onPackageInstallation` | During install (can modify install path) |
| `onSystemSettingExpansion` | When `${...}` placeholder is expanded |
| `onConfigSettingSave` | When config setting is saved |

---

### Custom Interception Points

```javascript
// In ModuleConfig.cfc configure()
interceptorSettings = {
    customInterceptionPoints: "onDeployStart,onDeployComplete"
};
```

Announce in a command:

```javascript
component {
    property name="interceptorService" inject="interceptorService";

    function run() {
        interceptorService.announceInterception( "onDeployStart", {
            env: "staging",
            timestamp: now()
        } );

        // ... do deploy work ...

        interceptorService.announceInterception( "onDeployComplete", {
            env: "staging",
            success: true
        } );
    }
}
```

---

## Injection DSL

```javascript
// CommandBox core
property inject="shell";
property inject="wirebox";
property inject="logbox";
property inject="logbox:logger:{this}";
property inject="interceptorService";

// Config
property inject="commandbox:moduleSettings:myModule";
property inject="commandbox:setting:myKey";

// Servers & packages
property inject="serverService";
property inject="packageService";
property inject="artifactService";
property inject="forgeBox";

// Models from other modules
property inject="MyService@myModule";

// Module metadata
property inject="moduleMapping@myModule";
property inject="modulePath@myModule";
```

---

## Sharing Your Module

```bash
# Initialize as a CommandBox module package
cd /path/to/my-module
package init slug="commandbox-myplugin" version=1.0.0
package set type=commandbox-modules
package set private=false

# Set ForgeBox API token
config set endpoints.forgebox.APIToken=your-token

# Publish to ForgeBox
publish
```

Anyone can then install it with:

```bash
install commandbox-myplugin
```