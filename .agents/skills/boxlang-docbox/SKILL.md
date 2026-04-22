---
name: boxlang-docbox
description: "Use this skill when generating API documentation for BoxLang or CFML projects with DocBox, including installation, CLI usage, programmatic configuration, HTML/JSON/UML/CommandBox output strategies, HTML themes, multiple sources, excludes patterns, and creating custom strategies. DocBox reads JavaDoc-style comments from your source code — see the code-documenter skill for annotation conventions."
---

# DocBox — API Documentation Generator

## Overview

DocBox automatically parses your BoxLang or CFML source code and generates
browsable API documentation in multiple output formats. It reads JavaDoc-style
`/** ... */` comments, class/property/function metadata, and inheritance
relationships.

**Published docs:** https://docbox.ortusbooks.com

---

## Installation

### BoxLang Module (Recommended)

```bash
# CommandBox web runtimes
box install bx-docbox

# BoxLang OS runtime
install-bx-module bx-docbox
```

### CommandBox Module

```bash
box install commandbox-docbox --saveDev
```

### CFML Application

```bash
# Install via CommandBox into your project
box install docbox --saveDev
```

Add a mapping if DocBox is not in the app root:

```javascript
// Application.bx / Application.cfc
this.mappings[ "/docbox" ] = expandPath( "./libraries/docbox" );
```

---

## CLI Usage (BoxLang, New in 5.0)

The `boxlang module:docbox` command is the fastest way to generate documentation:

```bash
# Minimal usage
boxlang module:docbox --source=/path/to/code --mapping=myapp --output-dir=/docs

# With title and excludes
boxlang module:docbox --source=/src \
                       --mapping=myapp \
                       --output-dir=/docs \
                       --project-title="My API" \
                       --excludes="(tests|build)"

# Use the frames (traditional three-panel) theme
boxlang module:docbox --source=/src --mapping=myapp \
                       --output-dir=/docs \
                       --theme=frames

# Multiple source mappings
boxlang module:docbox --mappings:models=/app/models \
                       --mappings:handlers=/app/handlers \
                       --output-dir=/docs

# Show all available options
boxlang module:docbox --help
```

---

## Programmatic Usage

### Single Strategy

```javascript
new docbox.DocBox()
    .addStrategy( "HTML", {
        projectTitle : "My API Documentation",
        outputDir    : expandPath( "./docs" ),
        theme        : "default"   // or "frames"
    } )
    .generate(
        source   = expandPath( "/app" ),
        mapping  = "app",
        excludes = "(tests|build)"
    )
```

### Multiple Strategies (Single Pass)

```javascript
new docbox.DocBox()
    .addStrategy( "HTML", {
        projectTitle : "My API",
        outputDir    : expandPath( "/docs/html" ),
        theme        : "default"
    } )
    .addStrategy( "JSON", {
        projectTitle : "My API",
        outputDir    : expandPath( "/docs/json" )
    } )
    .addStrategy( "XMI", {
        outputFile   : expandPath( "/docs/diagram.uml" )
    } )
    .generate(
        source   = expandPath( "/app" ),
        mapping  = "app",
        excludes = "(tests|specs|build)"
    )
```

### Multiple Source Directories

```javascript
new docbox.DocBox()
    .addStrategy( "HTML", {
        projectTitle : "My API",
        outputDir    : expandPath( "/docs" )
    } )
    .generate(
        source = [
            { dir: expandPath( "/app/models" ),   mapping: "app.models" },
            { dir: expandPath( "/app/handlers" ),  mapping: "app.handlers" },
            { dir: expandPath( "/plugins" ),       mapping: "plugins" }
        ],
        excludes = "(tests|specs)"
    )
```

---

## Output Strategies

### HTML Strategy

Generates a browsable SPA or frameset HTML site.

| Property | Required | Description |
|----------|----------|-----------|
| `outputDir` | Yes | Absolute path for output |
| `projectTitle` | Yes | Displayed in the page title |
| `projectDescription` | No | Sub-title / description text |
| `theme` | No | `"default"` (SPA) or `"frames"` (frameset). Default: `"default"` |

**Default theme** (SPA): Alpine.js routing, real-time search, method tabs (All/Public/Private), dark mode toggle, Bootstrap 5.

**Frames theme** (traditional): Three-panel frameset, left sidebar package navigation, dark mode, Bootstrap 5.

```javascript
// Modern SPA (default)
.addStrategy( "HTML", {
    projectTitle : "My API",
    outputDir    : expandPath( "/docs" ),
    theme        : "default"
} )

// Traditional frameset
.addStrategy( "HTML", {
    projectTitle : "My API",
    outputDir    : expandPath( "/docs" ),
    theme        : "frames"
} )
```

### JSON Strategy

Generates machine-readable JSON files for tooling and CI integration:

| Property | Required | Description |
|----------|----------|-----------|
| `outputDir` | Yes | Absolute path for output |
| `projectTitle` | No | Used in `overview-summary.json` |

```javascript
.addStrategy( "JSON", {
    projectTitle : "My API",
    outputDir    : expandPath( "/docs/json" )
} )
```

### UML/XMI Strategy

Generates an XMI file for use with UML diagram tools:

| Property | Required | Description |
|----------|----------|-----------|
| `outputFile` | Yes | Absolute path to the output `.uml` / `.xmi` file |

```javascript
.addStrategy( "XMI", {
    outputFile : expandPath( "/docs/diagram.uml" )
} )
```

### CommandBox Strategy

Generates CommandBox-compatible command documentation:

```javascript
.addStrategy( "CommandBox", {
    outputDir : expandPath( "/commandbox-docs" )
} )
```

---

## Excludes Pattern

The `excludes` parameter is a **regular expression** evaluated against each
class name and path. Matching classes are skipped.

```javascript
// Skip tests and build output
excludes = "(tests|specs|build)"

// Skip specific packages
excludes = "(com\.example\.internal|legacy)"

// Skip multiple patterns
excludes = "(tests|mocks|stubs|helpers)"
```

---

## Custom Output Strategy

Extend `AbstractTemplateStrategy` (which implements `IStrategy`) to create your
own output format:

```javascript
/**
 * Markdown documentation strategy for DocBox.
 */
component extends="docbox.strategy.AbstractTemplateStrategy" {

    /**
     * @param metadata  Query with columns: package, name, metadata, type, extends, implements
     */
    IStrategy function run( required query metadata ) {
        var outputDir = variables.properties.outputDir

        ensureDirectory( outputDir )

        for ( var row in arguments.metadata ) {
            var mdContent = buildMarkdown( row.metadata )
            writeTemplate(
                path     = outputDir & "/" & row.name & ".md",
                template = mdContent
            )
        }

        return this
    }

    private string function buildMarkdown( required struct meta ) {
        return "# #meta.name#" & chr( 10 ) & meta.hint
    }

}
```

Register and use it:

```javascript
new docbox.DocBox()
    .addStrategy( "docbox.strategy.MarkdownStrategy", {
        outputDir : expandPath( "/docs/markdown" )
    } )
    .generate( source = expandPath( "/app" ), mapping = "app" )
```

---

## Backwards Compatibility

Specifying the full class path or a single constructor strategy still works:

```javascript
// Full class path
variables.docbox = new docbox.DocBox(
    strategy   = "docbox.strategy.uml2tools.XMIStrategy",
    properties = { outputFile : expandPath( "/docs/diagram.uml" ) }
)

// Or short alias on addStrategy
docbox.addStrategy( "docbox.strategy.api.HTMLAPIStrategy", { ... } )
```

---

## CI/CD Integration (GitHub Actions)

```yaml
- name: Generate API docs
  run: |
    boxlang module:docbox \
      --source=${{ github.workspace }}/src \
      --mapping=myapp \
      --output-dir=${{ github.workspace }}/docs \
      --project-title="My API" \
      --excludes="(tests|build)"

- name: Upload docs artifact
  uses: actions/upload-artifact@v4
  with:
    name: api-docs
    path: docs/
```

---

## Checklist

- [ ] DocBox installed: `box install bx-docbox` or `install-bx-module bx-docbox`
- [ ] All public functions have `/** ... */` doc comments (see `code-documenter` skill)
- [ ] `@doc.type` added for `array`/`struct`/`any` return and argument types
- [ ] `excludes` regex covers tests, build output, and internal packages
- [ ] `outputDir` is an absolute path (use `expandPath()`)
- [ ] Theme chosen: `"default"` for SPA or `"frames"` for traditional three-panel layout
- [ ] Multiple strategies added in one `DocBox` instance for a single-pass multi-format run