---
name: commandbox-task-runners
description: "Use this skill for CommandBox task runners: creating task CFCs, targets, passing parameters, lifecycle events (preTask/postTask/onError), interactive jobs with job DSL, progress bars, async/threading with AsyncManager, watching files, running commands from tasks, shell integration, property files, downloading files, sending email from tasks, and task target dependencies."
---

# CommandBox Task Runners

## Overview

Task Runners are CFML/BoxLang CFCs that automate build processes, migrations, and workflows. They are analogous to Ant/Gradle tasks but written in CFML. Tasks live in your project folder and are not globally registered — they are context-aware based on your current working directory.

```bash
# Create a default task.cfc
task create

# Create and open in editor
task create --open

# Run the default task
task run

# Run a specific task file
task run path/to/myTask

# Run a specific target
task run path/to/myTask myTarget
```

---

## Task Anatomy

```javascript
/**
 * My build task
 */
component {

    // WireBox DI available
    property name="artifactService" inject="artifactService";

    /**
     * Default target — runs when no target specified
     */
    function run() {
        print.greenLine( "Build complete!" );
    }

    /**
     * Named target
     */
    function build() {
        print.line( "Building..." );
        command( "package version patch" ).run();
        print.greenLine( "Built!" );
    }

    /**
     * Target with parameters
     */
    function deploy( required string environment, boolean verbose=false ) {
        print.line( "Deploying to #environment#" );
        if ( verbose ) {
            print.yellowLine( "Verbose mode on" );
        }
    }

}
```

**Default conventions**:
- Default task file: `task.cfc` in current directory
- Default target method: `run()`

```bash
task run                              # runs task.cfc::run()
task run build                        # runs task.cfc::build()
task run workbench/build              # runs workbench/build.cfc::run()
task run workbench/build createZips   # runs workbench/build.cfc::createZips()
```

---

## Passing Parameters

### Named parameters (recommended)

```bash
task run fun greet :name=Brad :verbose=true
```

### Positional parameters

```bash
task run fun greet Brad true
```

### Boolean flags

```bash
task run --:verbose
task run --no:verbose
task run --!:verbose
```

### Dynamic values

```bash
task run :message=`cat message.txt`
task run taskFile=deploy :env=${DEPLOY_ENV:staging}
```

---

## Lifecycle Events

Special methods that fire automatically around target execution:

```javascript
component {

    // Before ANY target
    function preTask( string target, struct taskArgs ) {
        print.line( "Starting task: #target#" );
    }

    // After ANY target
    function postTask( string target, struct taskArgs ) {
        print.line( "Finished task: #target#" );
    }

    // Wrap ANY target (must call invokeUDF to run the target)
    function aroundTask( string target, struct taskArgs, any invokeUDF ) {
        var startTime = getTickCount();
        local.result = invokeUDF();
        print.line( "Elapsed: #(getTickCount()-startTime)#ms" );
        return local.result;  // IMPORTANT: return result to preserve exit codes
    }

    // Before specific target
    function preRun() {
        print.line( "Before run" );
    }

    // After specific target
    function postRun() {
        print.line( "After run" );
    }

    // Fires regardless of success/failure
    function onComplete( string target, struct taskArgs ) {
        print.line( "Always fires" );
    }

    // Fires on success only
    function onSuccess( string target, struct taskArgs ) {
        print.greenLine( "Task succeeded!" );
    }

    // Fires on any failure (no exception object)
    function onFail( string target, struct taskArgs ) {
        print.redLine( "Task failed!" );
    }

    // Fires only on unhandled exceptions — has exception object
    function onError( string target, struct taskArgs, any exception ) {
        print.redLine( "Exception: #exception.message#" );
    }

    // Fires when Ctrl+C is pressed
    function onCancel( string target, struct taskArgs ) {
        print.yellowLine( "Task cancelled by user" );
    }

    function run() {
        print.line( "Hello!" );
    }

}
```

**Limit lifecycle events to specific targets:**

```javascript
this.preTask_only = "run,build";       // only fire for run and build
this.postTask_except = "cleanUp";      // fire for all except cleanUp
```

---

## Task Target Dependencies

```javascript
component {

    // Define dependencies
    this.depends_build = "clean,compile";

    function clean() {
        directoryDelete( "dist", true );
    }

    function compile() {
        // compile step
    }

    // build will run clean, then compile, then itself
    function build() {
        zip( action="zip", file="dist/app.zip", source="src" );
    }

}
```

---

## Interactive Jobs (Progress Display)

```javascript
function run() {
    // Start a named job
    job.start( "Deploying application" );

    job.addLog( "Connecting to server..." );
    // ... work ...

    job.addSuccessLog( "Connected!" );
    job.addWarnLog( "Using staging credentials" );
    job.addErrorLog( "Warning: old config detected" );
    job.addLog( "Uploading files..." );

    if ( success ) {
        job.complete();        // green success line
    } else {
        job.error( "Deploy failed: connection refused" );  // red error
    }

    // Nested jobs
    job.start( "Running migrations", lineSize=10 );
    job.addLog( "Migration 001..." );
    job.addLog( "Migration 002..." );
    job.complete();
}
```

---

## Progress Bar

```javascript
function run() {
    var total = 100;
    progressBar.update( percent=0, currentCount=0, totalCount=total );

    for ( var i = 1; i <= total; i++ ) {
        // do work
        progressBar.update( percent=int((i/total)*100), currentCount=i, totalCount=total );
    }

    print.line();  // newline after bar
}
```

---

## Print Helper (ANSI Output)

```javascript
function run() {
    print.line( "Normal text" );
    print.greenLine( "Success message" );
    print.redLine( "Error message" );
    print.yellowLine( "Warning" );
    print.cyanLine( "Info" );
    print.boldLine( "Bold text" );
    print.boldGreenLine( "Bold green" );

    // Inline (no newline)
    print.green( "Processing..." );
    print.line();

    // Table output
    print.table(
        headers = [ "Name", "Version", "Status" ],
        data = [
            [ "coldbox", "7.0.0", "active" ],
            [ "testbox", "5.0.0", "active" ]
        ]
    );
}
```

---

## Running Commands from Tasks

```javascript
function run() {
    // Run a CommandBox command
    command( "install coldbox" ).run();

    // Capture output
    var result = command( "package show version" ).run( returnOutput=true );
    print.line( "Version: #result#" );

    // Chain commands
    command( "server stop" )
        .params( name="myApp" )
        .run();

    // Run OS commands with !
    command( "!git pull origin main" ).run();

    // Check exit code
    var exitCode = command( "testbox run" ).run( returnExitCode=true );
    if ( exitCode != 0 ) {
        error( "Tests failed!" );
    }
}
```

---

## Shell Integration

```javascript
function run() {
    // Run OS shell commands
    var result = shell( "ls -la" );

    // On Windows
    var result = shell( "dir" );

    // Run with specific working directory
    shell( command="npm install", dir="/my/app" );
}
```

---

## Async / Threading

```javascript
function run() {
    // Use cfthread with unique names
    var threadName = createGUID();
    cfthread( action="run" name=threadName ) {
        // thread body
    }
    cfthread( action="join" name=threadName );

    // Async with AsyncManager
    var results = async().all(
        () => command( "install module1" ).run( returnOutput=true ),
        () => command( "install module2" ).run( returnOutput=true )
    ).get();
}
```

---

## Watching Files

```javascript
function run() {
    // Watch and re-run task on changes
    watch()
        .paths( "**.cfc,**.cfm" )
        .inDirectory( getCWD() )
        .withDelay( 500 )
        .onChange( function() {
            command( "testbox run" ).run();
        } )
        .start();
}
```

---

## Property Files

```javascript
function run() {
    // Read .properties file
    var props = propertyFile( getCWD() & "/config.properties" );
    var dbHost = props[ "db.host" ];

    // Write properties
    props[ "build.version" ] = "2.0.0";
    props.store();
}
```

---

## Downloading Files

```javascript
function run() {
    // Download a file with progress
    var filePath = getCWD() & "/downloads/package.zip";
    progressable
        .download( "https://example.com/package.zip", filePath )
        .withProgressBar()
        .start();
}
```

---

## Sending Email from Tasks

```javascript
function run() {
    // Send email using bx-mail module (if installed)
    bx:mail
        from="build@example.com"
        to="team@example.com"
        subject="Build #packageVersion# complete"
        server="#mailServer#"
        port=587
    {
        writeOutput( "The build finished successfully." );
    }
}
```

---

## Cancelling Long Tasks

```javascript
function run() {
    // Check if user has pressed Ctrl+C
    for ( var i = 1; i <= 1000; i++ ) {
        // Check for cancellation
        if ( isCancelled() ) {
            print.yellowLine( "Cancelled at step #i#" );
            return;
        }
        // do work
    }
}

function onCancel() {
    // cleanup on Ctrl+C
    print.redLine( "Cleaning up temporary files..." );
    directoryDelete( "/tmp/build", true );
}
```

---

## Hitting a Database

```javascript
component {

    // Inject a datasource via WireBox
    property name="datasource" inject="coldbox:datasource:myDS";

    function run() {
        var q = queryExecute(
            "SELECT * FROM users WHERE active = :active",
            { active: { value: 1, cfsqltype: "integer" } },
            { datasource: "myDS" }
        );
        q.each( function( row ) {
            print.line( "#row.name# - #row.email#" );
        } );
    }

}
```

---

## Loading Ad-Hoc JARs

```javascript
component {

    function run() {
        // Load a JAR into the class path
        classLoad( getCWD() & "/lib/mylib.jar" );
        var obj = createObject( "java", "com.example.MyClass" );
    }

}
```

---

## Loading Ad-Hoc Modules

```javascript
component {

    function run() {
        // Temporarily load a module
        modulesConfig = { "myTempModule": { "path": getCWD() & "/myModule" } };
        moduleService.registerAndActivateModule( "myTempModule", getCWD() & "/myModule" );

        // Use module functionality
        var svc = getInstance( "myService@myTempModule" );
        svc.doWork();
    }

}
```