---
name: boxlang-runtime-google-cloud-functions
description: "Use this skill when building, testing, or deploying BoxLang applications on Google Cloud Functions Gen 2, including handler structure, FunctionRunner entry point, URI routing, environment variables, local development with the GCF invoker, and debugging."
---

# BoxLang on Google Cloud Functions

## Overview

BoxLang runs on Google Cloud Functions Gen 2 via the Java 21 runtime. The
`FunctionRunner` bridge handles HTTP request mapping, handler routing, class
caching, and response serialization — so you focus on writing BoxLang handler files.

---

## Architecture

```
HTTP Request
    → GCF Gen 2 (java21 runtime)
    → FunctionRunner (HttpFunction)
    → RequestMapper (HttpRequest → BoxLang event struct)
    → Route resolution (first URI segment → PascalCase → .bx file)
    → Handler compilation + class cache (warm invocations)
    → Method dispatch (run() or x-bx-function header)
    → BoxLang handler execution
    → ResponseMapper (response struct → HttpResponse)
```

**Runtime entry point:**

```
ortus.boxlang.runtime.gcp.FunctionRunner
```

---

## Starter Project

```bash
git clone https://github.com/ortus-boxlang/boxlang-starter-google-functions.git
cd boxlang-starter-google-functions

# Verify environment
./gradlew clean test
```

---

## Handler File Convention

Handlers live in `src/main/bx/`. The URI path maps to a PascalCase `.bx` file:

| Request URI | Resolved handler |
|------------|----------------|
| `GET /`     | `src/main/bx/Index.bx` |
| `GET /orders` | `src/main/bx/Orders.bx` |
| `POST /users` | `src/main/bx/Users.bx` |

Each handler file is a BoxLang class with a `run()` method:

```boxlang
// src/main/bx/Orders.bx
class {

    /**
     * Main GCF handler.
     *
     * @param event    HTTP event struct: { method, path, headers, body, queryString }
     * @param context  GCF context info
     * @param response Response struct to populate: { statusCode, body, headers }
     */
    function run( event, context, response ){
        response.statusCode = 200
        response.headers    = { "Content-Type": "application/json" }
        response.body       = serializeJSON({
            orders:  orderService.list(),
            total:   orderService.count()
        })
    }

}
```

---

## Calling Specific Methods (Header Routing)

Use the `x-bx-function` header to call a specific method on a handler class:

```bash
# Calls Orders.bx run() (default)
curl http://localhost:9099/orders

# Calls Orders.bx createOrder()
curl -H "x-bx-function: createOrder" -X POST http://localhost:9099/orders \
    -H "Content-Type: application/json" \
    -d '{"productId": 1, "qty": 2}'
```

In the handler:

```boxlang
class {
    function run( event, context, response ){
        // Default — list orders
    }

    function createOrder( event, context, response ){
        var data = deserializeJSON( event.body )
        var order = orderService.create( data )
        response.statusCode = 201
        response.body = serializeJSON( order )
    }
}
```

---

## Event Struct

The incoming HTTP request is mapped to an `event` struct:

```boxlang
// event properties
event.method       // "GET", "POST", "PUT", etc.
event.path         // "/orders/42"
event.headers      // Struct of HTTP headers
event.queryString  // Struct of parsed query parameters
event.body         // Raw request body string
event.json         // Parsed JSON body (if Content-Type: application/json)
```

---

## Environment Variables

| Variable | Description |
|----------|-----------|
| `BOXLANG_GCP_ROOT` | Root directory for handler `.bx` files. Default: `src/main/bx` |
| `BOXLANG_GCP_DEBUGMODE` | Disable class caching for hot reload during development |
| `BOXLANG_GCP_CONFIG` | Path to a custom `boxlang.json` configuration |

---

## Local Development

```bash
# Start local GCF server (port 9099)
./gradlew runFunction

# Custom port
./gradlew runFunction -PtestPort=8080

# Enable debug mode (disables class cache for hot reload)
./gradlew runFunction -PdebugMode=true

# Custom function root
./gradlew runFunction -PfunctionRoot=/absolute/path/to/bx
```

Expected startup banner:

```
================================================================
 BoxLang GCF Function Invoker
 Listening on  : http://localhost:9099
 Function root : .../src/main/bx
 Debug mode    : false
 Press Ctrl+C to stop.
================================================================
```

---

## Testing Locally

```bash
# Simple GET
curl http://localhost:9099/

# POST with JSON payload
curl -X POST http://localhost:9099/orders \
    -H "Content-Type: application/json" \
    -d '{"productId": 1, "qty": 2}'

# Header-routed method
curl -H "x-bx-function: anotherMethod" http://localhost:9099/

# From a sample event file
curl -X POST http://localhost:9099/ \
    -H "Content-Type: application/json" \
    -d @workbench/sampleRequests/event-local.json
```

---

## Cold Start, Warm Start, Debug Mode

| Mode | Behavior |
|------|---------|
| Cold start | Runtime initializes; first request is slower |
| Warm invocation | Compiled handler class reused — fast |
| Debug mode ON | Class cache disabled; edits picked up immediately |
| Debug mode OFF (production) | Maximum performance via class caching |

---

## Deploying to GCP

```bash
# Authenticate
gcloud auth login
gcloud config set project YOUR_PROJECT_ID

# Build the deployment ZIP
./gradlew buildZip

# Deploy via gcloud
gcloud functions deploy myBoxLangFunction \
    --gen2 \
    --runtime java21 \
    --region us-central1 \
    --entry-point ortus.boxlang.runtime.gcp.FunctionRunner \
    --source build/distributions/function.zip \
    --trigger-http \
    --allow-unauthenticated \
    --memory 512MB \
    --min-instances 0 \
    --max-instances 10
```

---

## Portability with AWS Lambda

Handler patterns are intentionally compatible between BoxLang GCF and AWS Lambda:

| Feature | GCF | AWS Lambda |
|---------|-----|-----------|
| Handler class | `FunctionRunner` | `LambdaRunner` |
| Handler method | `run()` | `run()` |
| Method routing header | `x-bx-function` | `x-bx-function` |
| Event struct | Same structure | Same structure |
| Debug mode env var | `BOXLANG_GCP_DEBUGMODE` | `BOXLANG_LAMBDA_DEBUGMODE` |

Many handlers can be shared between GCF and Lambda with minimal changes.

---

## Production Checklist

- [ ] `BOXLANG_GCP_DEBUGMODE=false` in production
- [ ] Handler files under `src/main/bx/` named in PascalCase matching URI segments
- [ ] Secrets injected as environment variables (not in code)
- [ ] Memory: 512MB minimum; 1GB+ for database-heavy functions
- [ ] Min-instances configured for latency-sensitive paths
- [ ] Integration tests passing: `./gradlew clean test`
- [ ] Deployment ZIP built fresh before each deploy: `./gradlew buildZip`