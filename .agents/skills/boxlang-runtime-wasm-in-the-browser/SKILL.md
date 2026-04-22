---
name: boxlang-runtime-wasm-in-the-browser
description: "Use this skill when compiling BoxLang to WebAssembly or JavaScript ES modules for use in web browsers and Node.js using MatchBox's --target js or --target wasm flags, integrating with HTML pages, bundlers (Webpack/Vite), and Node.js projects."
---

# BoxLang WASM in the Browser

## Overview

MatchBox can compile BoxLang source to browser-compatible output via two modes:

| Mode | Flag | Output | Best For |
|------|------|--------|---------|
| ES Module (recommended) | `--target js` | `.js` + `.wasm` | Browsers, Node.js, bundlers |
| Raw WASM | `--target wasm` | `.wasm` | Custom loaders, advanced bundling |

All top-level BoxLang functions are automatically exported, regardless of access modifier.

---

## Mode 1: `--target js` — ES Module (Recommended)

```bash
matchbox --target js my_lib.bxs
# Output: my_lib.js + my_lib.wasm
```

The generated `.js` file is an ES module wrapper. The `.wasm` file is loaded automatically.

### Browser Usage

```html
<!DOCTYPE html>
<html>
<head>
    <title>BoxLang in the Browser</title>
</head>
<body>
    <script type="module">
        import { greet, calculateTotal } from './my_lib.js'

        const message = greet("World")
        document.getElementById("output").textContent = message

        const total = calculateTotal([ 10.99, 5.49, 3.00 ])
        console.log("Total:", total)
    </script>

    <div id="output"></div>
</body>
</html>
```

### BoxLang source (`my_lib.bxs`)

```js
// All top-level functions are exported automatically
function greet( name ) {
    return "Hello, " & name & "!"
}

function calculateTotal( prices ) {
    return prices.reduce( ( sum, price ) -> sum + price, 0 )
}
```

### Node.js Usage

```js
// node-app.mjs
import { greet, calculateTotal } from './my_lib.js'

console.log( greet( "Node.js" ) )
console.log( calculateTotal([ 10.99, 5.49, 3.00 ]) )
```

```bash
node node-app.mjs
```

---

## Mode 2: `--target wasm` — Raw WASM

```bash
matchbox --target wasm my_lib.bxs
# Output: my_lib.wasm
```

Use this when you need manual control over WASM loading, or integration with Webpack/Vite:

```js
// Manual loading
const { instance } = await WebAssembly.instantiateStreaming(
    fetch('/my_lib.wasm'),
    {}
)
const { greet } = instance.exports
```

### Vite Integration

```js
// vite.config.js
import { defineConfig } from 'vite'

export default defineConfig({
    assetsInclude: ['**/*.wasm'],
    plugins: []
})
```

```js
// main.js — with Vite WASM handling
import wasmUrl from './my_lib.wasm?url'

const response = await fetch(wasmUrl)
const { instance } = await WebAssembly.instantiateStreaming(response)
const { greet } = instance.exports
```

---

## Function Export Rules

- **All top-level functions** are exported automatically (no `public` annotation needed)
- Functions defined inside class bodies are NOT exported
- Keep exported BoxLang functions at the top level of the `.bxs` file

```js
// ✅ Exported — top-level function
function add( a, b ) {
    return a + b
}

// ✅ Exported — top-level function
function formatCurrency( amount, currency = "USD" ) {
    return currency & " " & numberFormat( amount, "2" )
}

// ❌ NOT exported — inside a class
class MyLib {
    function helper() { ... }
}
```

---

## Browser Compatibility

| Browser | ES Module WASM Support |
|---------|----------------------|
| Chrome 61+ | ✅ |
| Firefox 60+ | ✅ |
| Safari 14.1+ | ✅ |
| Edge 79+ | ✅ |
| Node.js 14+ | ✅ |

---

## Bundler Integration

### Webpack 5

```js
// webpack.config.js
module.exports = {
    experiments: {
        asyncWebAssembly: true
    }
}
```

```js
// app.js
const myLib = await import('./my_lib.js')
myLib.greet("Webpack")
```

### Vite + ES Module

```bash
# Place my_lib.js + my_lib.wasm in /public or /src/assets
# Import as ES module (no special config needed for --target js output)
```

```js
import { greet } from '/my_lib.js'
greet("Vite")
```

---

## Development Workflow

```bash
# 1. Write BoxLang logic
cat > utils.bxs << 'EOF'
function slugify( text ) {
    return text.lCase().reReplace( "[^a-z0-9]+", "-", "all" ).trim( "-" )
}

function truncate( text, maxLen = 100 ) {
    return text.len() > maxLen ? text.left( maxLen ) & "..." : text
}
EOF

# 2. Compile to ES module
matchbox --target js utils.bxs

# 3. Serve (for browser testing — WASM requires HTTP, not file://)
npx serve .

# 4. Import in your app
```

> **Important:** WASM files must be served over HTTP (not `file://`). Use a local dev server like `npx serve`, Vite, or a browser extension during development.

---

## Checklist

- [ ] Use `--target js` for browser and Node.js integration (simpler than raw WASM)
- [ ] Keep exported functions at the top level of the `.bxs` file
- [ ] Serve via HTTP (not `file://`) — browsers block WASM from `file://` origins
- [ ] For bundlers (Vite/Webpack), copy both `.js` and `.wasm` output files together
- [ ] Use `--target wasm` only when you need manual loader control or advanced bundling
- [ ] Test in Node.js first for faster iteration before testing in the browser