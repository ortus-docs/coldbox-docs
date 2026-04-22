---
name: boxlang-runtime-cli-scripting
description: "Use this skill when writing BoxLang CLI scripts and classes, handling command-line arguments, using the BoxLang REPL, running action commands (compile, cftranspile, featureaudit), detecting runtime context, and leveraging CLI-specific built-in functions."
---

# BoxLang CLI Scripting

## Overview

The `boxlang` binary can run scripts, classes, templates, and inline code directly from the command line. It supports multiple file types, shebang lines, structured argument parsing, and a built-in REPL.

---

## Supported File Types

| Extension | Type | Entry Point |
|-----------|------|-------------|
| `.bx` | BoxLang class | `main(args=[])` method |
| `.bxs` | BoxLang script | top-level code |
| `.bxm` | BoxLang template | top-level markup/code |
| `.cfs` / `.cfm` | CFML (via bx-compat-cfml) | top-level code |
| `.sh` | Shell script with shebang | shebang `#!/usr/bin/env boxlang` |

---

## Running Files

```bash
# Class with main() method
boxlang MyApp.bx

# Script file
boxlang process.bxs --config=prod.json --debug

# Template file
boxlang render.bxm

# Inline code
boxlang --bx-code "println( 'Hello!' )"

# From stdin
echo "println( 'piped!' )" | boxlang

# REPL (no arguments)
boxlang
```

---

## Class Entry Point (`main`)

Use `.bx` files with a `main()` method for structured CLI applications:

```js
class MyApp {
    static function main( args = [] ) {
        var parsed = CLIGetArgs()
        println( "Options: " & parsed.options.toString() )
        println( "Positionals: " & parsed.positionals.toString() )
    }
}
```

---

## Argument Parsing

BoxLang parses CLI arguments automatically. Access them via `CLIGetArgs()` or `server.cli.parsed`:

```js
// In a .bxs script
var args = CLIGetArgs()
// args = { options: { debug: true, config: "prod.json" }, positionals: [ "file.txt" ] }

var debug = args.options.debug ?: false
var config = args.options.config ?: "default.json"
```

### Argument Formats

| Format | Parsed As | Example |
|--------|-----------|---------|
| `--option` | `options.option = true` | `--debug` |
| `--option=value` | `options.option = "value"` | `--config=prod.json` |
| `--option="value"` | `options.option = "value"` | `--message="Hello"` |
| `-o=value` | `options.o = "value"` | `-c=config.json` |
| `-o` | `options.o = true` | `-v` |
| `--!option` | `options.option = false` | `--!verbose` |
| `--no-option` | `options.option = false` | `--no-debug` |
| `-abc` | `a=true,b=true,c=true` | `-vdx` |

### Example

```bash
boxlang myscript.bxs --debug --!verbose --config=prod.json -o='/tmp/out' report.txt
```

Results in:

```js
{
    "options": {
        "debug": true,
        "verbose": false,
        "config": "prod.json",
        "o": "/tmp/out"
    },
    "positionals": [ "report.txt" ]
}
```

---

## Shebang Scripts

Make scripts directly executable with a shebang line:

```bash
#!/usr/bin/env boxlang
println( "I run directly!" )
var args = CLIGetArgs()
println( args.toString() )
```

```bash
chmod +x myscript.bxs
./myscript.bxs --name=World
```

---

## CLI Built-In Functions

| BIF | Returns | Description |
|-----|---------|-------------|
| `CLIGetArgs()` | `struct` | Parsed options + positionals |
| `CLIRead( [prompt] )` | `any` | Read a line of input from stdin |
| `CLIClear()` | `void` | Clear the console screen |
| `CLIExit( [exitCode=0] )` | — | Exit with given code (`System.exit()`) |

```js
// Interactive prompt
var name = CLIRead( "Enter your name: " )
println( "Hello, " & name )

// Exit with error code
if ( !fileExists( configFile ) ) {
    println( "Error: config not found" )
    CLIExit( 1 )
}
```

---

## CLI Flags

| Flag | Env Variable | Description |
|------|-------------|-------------|
| `--bx-debug` | `BOXLANG_DEBUG` | Enable debug output |
| `--bx-config <path>` | `BOXLANG_CONFIG` | Path to `boxlang.json` config file |
| `--bx-home <path>` | `BOXLANG_HOME` | Override BoxLang home directory |
| `--bx-code <code>` | — | Execute inline BoxLang code string |
| `--bx-printAST` | `BOXLANG_PRINTAST` | Print the AST for the script |
| `--bx-transpile` | `BOXLANG_TRANSPILE` | Transpile BoxLang to Java source |

```bash
# Run with custom config and debug
boxlang --bx-debug --bx-config ./config/boxlang.json myapp.bxs

# Print the AST
boxlang --bx-printAST myscript.bxs

# Inline execution
boxlang --bx-code "println( now() )"
```

---

## Action Commands

Run via `boxlang <action> [options]` — these compile, convert, or audit code.

### `compile` — Compile to bytecode

```bash
boxlang compile --source ./src --target ./compiled
```

Options: `--source <path>`, `--target <path>`, `--recurse true/false`

### `cftranspile` — Transpile CFML to BoxLang

```bash
boxlang cftranspile --source ./legacy-cfml --target ./bx-src
```

Options: `--source <path>`, `--target <path>`, `--recurse true/false`

### `featureaudit` — Audit CFML compatibility

```bash
boxlang featureaudit --source ./legacy-cfml
```

Reports unsupported or incompatible CFML patterns.

### `schedule` — Run a scheduler file

```bash
boxlang schedule ./schedulers/MainScheduler.bx
```

Runs continuously until `Ctrl+C`. File must be a `.bx` class with scheduler definitions.

---

## Runtime Detection

Detect the execution context in BoxLang code:

```js
// CLI mode vs web server vs JAR mode
if ( server.boxlang.cliMode ) {
    println( "Running in CLI mode" )
} else if ( server.boxlang.jarMode ) {
    println( "Running as embedded JAR" )
}

// Access parsed CLI arguments
var parsed = server.cli.parsed
// { options: {}, positionals: [] }

// Runtime info
println( server.boxlang.runtimeHome )
println( server.coldfusion.productVersion )  // BoxLang version
```

---

## Available Scopes in CLI Mode

| Scope | Description |
|-------|-------------|
| `application` | Application-level shared state |
| `request` | Current request/execution context |
| `server` | Server/runtime information |
| `server.cli` | CLI-specific runtime metadata |
| `server.cli.parsed` | Equivalent to `CLIGetArgs()` return |

---

## REPL Mode

Start with no arguments for an interactive session:

```bash
boxlang
# BoxLang> println( "Hello!" )
# Hello!
# BoxLang> 1 + 1
# 2
# BoxLang> :history    (show history)
# BoxLang> :clear      (clear screen)
# BoxLang> Ctrl+C      (exit)
```

- Supports multi-line input (continues on unbalanced braces)
- `!!` repeats last command; `!n` repeats command n
- `:dark` / `:light` switch color themes

---

## Example Script Patterns

### Script with validation

```js
// validate-args.bxs
var args = CLIGetArgs()

if ( args.positionals.isEmpty() ) {
    println( "Usage: boxlang validate-args.bxs <filename> [--verbose]" )
    CLIExit( 1 )
}

var file = args.positionals[1]
var verbose = args.options.verbose ?: false

if ( !fileExists( file ) ) {
    println( "Error: File not found: " & file )
    CLIExit( 2 )
}

println( "Processing: " & file )
```

### Class with structured main

```js
// DataProcessor.bx
class DataProcessor {
    static function main( args = [] ) {
        var opts = CLIGetArgs().options
        var inputFile  = opts.input  ?: throw( "--input is required" )
        var outputFile = opts.output ?: "output.json"
        var debug      = opts.debug  ?: false

        if ( debug ) println( "Input:  " & inputFile )
        if ( debug ) println( "Output: " & outputFile )

        // process...
    }
}
```

---

## Checklist

- [ ] Use `CLIGetArgs()` for all argument access (not raw `args` array)
- [ ] Validate required arguments and call `CLIExit( 1 )` on error
- [ ] Use `--bx-config` / `BOXLANG_CONFIG` to separate config from code
- [ ] Use `server.boxlang.cliMode` for runtime-aware conditional logic
- [ ] Prefer `.bx` classes with `main()` for reusable, testable entry points
- [ ] Use `#!/usr/bin/env boxlang` shebang for directly executable scripts
- [ ] Use `CLIRead()` for interactive prompts; `CLIClear()` for UI scripts