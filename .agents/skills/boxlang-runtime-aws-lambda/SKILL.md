---
name: boxlang-runtime-aws-lambda
description: "Use this skill when building, deploying, or debugging BoxLang applications on AWS Lambda — including Lambda.bx structure, handler conventions, environment variables, SAM CLI testing, performance optimization, connection pooling, multi-function routing, and the boxlang-starter-aws-lambda project."
---

# BoxLang on AWS Lambda

## Overview

The BoxLang AWS Runtime provides a pre-built Java handler for serverless Lambda
functions. You write BoxLang classes; the runtime handles request/response
lifecycle, serialization, logging, and error management.

---

## Handler Configuration

Set this as your Lambda handler in AWS:

```
ortus.boxlang.runtime.aws.LambdaRunner::handleRequest
```

The runtime automatically:
- Deserializes the incoming JSON event into a BoxLang `Struct`
- Calls your `Lambda.bx` class `run()` method
- Serializes the return value back to JSON
- Manages error handling and logging

---

## Lambda.bx Convention

The runtime looks for `Lambda.bx` in your deployment package root and calls `run()`:

```boxlang
class {

    /**
     * The main Lambda handler function.
     *
     * @param event    The incoming event struct (deserialized from JSON)
     * @param context  The AWS Lambda context object (Java LambdaContext)
     * @param response A pre-built response struct you can populate:
     *                 { statusCode: 200, body: "", headers: {} }
     */
    function run( event, context, response ){
        // Simple return value — auto-serialized to JSON
        return {
            statusCode: 200,
            body: {
                message: "Hello from BoxLang Lambda!",
                input:   event
            }
        }
    }

}
```

### Response Convention

You can either `return` a value or populate the `response` struct:

```boxlang
function run( event, context, response ){
    // Option 1: return a value (auto-serialized)
    return { statusCode: 200, body: "OK" }

    // Option 2: populate response struct
    response.statusCode = 200
    response.body       = serializeJSON({ message: "Done" })
    response.headers    = { "Content-Type": "application/json" }
}
```

---

## Application Lifecycle (`Application.bx`)

Place `Application.bx` in your deployment package for lifecycle hooks:

```boxlang
class {
    this.name = "MyLambda"

    // Called once on cold start — initialize resources here
    boolean function onApplicationStart(){
        application.db = createObject("component","DataService").init()
        return true
    }

    // Called on every request before run()
    boolean function onRequestStart( required string targetPage ){
        return true
    }
}
```

---

## Project Structure (Starter Template)

Use the official starter: `https://github.com/ortus-boxlang/boxlang-starter-aws-lambda`

```
/src
  /main
    /bx
      Application.bx     -- Lifecycle class
      Lambda.bx          -- Your Lambda handler
    /resources
      boxlang.json        -- BoxLang runtime config
      boxlang_modules/    -- Installed BoxLang modules
  /test
    /java
      /com/myproject
        LambdaRunnerTest.java   -- Integration tests
        TestContext.java
        TestLogger.java
/workbench
  config.env              -- Default env config
  config.local.env        -- Local overrides (gitignored)
  sampleEvents/           -- Test event payloads (.json files)
  template.yml            -- SAM template for local testing + deploy
  *.sh                    -- Deploy/management scripts
/box.json                 -- BoxLang module dependencies
/build.gradle             -- Build config (shaded JAR)
```

---

## Environment Variables

| Variable | Description |
|----------|-----------|
| `BOXLANG_LAMBDA_CLASS` | Absolute path to Lambda class. Default: `/var/task/Lambda.bx` |
| `BOXLANG_LAMBDA_DEBUGMODE` | Enable debug mode + performance metrics (`true`/`false`) |
| `BOXLANG_LAMBDA_CONFIG` | Path to custom `boxlang.json`. Default: `/var/task/boxlang.json` |
| `BOXLANG_LAMBDA_CONNECTION_POOL_SIZE` | Database connection pool size. Default: `2` |
| `LAMBDA_TASK_ROOT` | Lambda deployment root. Default: `/var/task` |

Any `BOXLANG_*` env variable also maps to `boxlang.json` config overrides.

---

## Performance Enhancements

### Class Compilation Caching

Lambda classes are compiled and cached on the first (cold start) invocation.
Warm invocations reuse the cached bytecode — no re-compilation overhead.

Disable caching in development (debug mode):
```bash
BOXLANG_LAMBDA_DEBUGMODE=true
```

### Connection Pooling

Database connections are pooled and reused across warm invocations:

```bash
BOXLANG_LAMBDA_CONNECTION_POOL_SIZE=5
```

### Cold Start Optimization Tips

1. Use `Application.bx` `onApplicationStart()` to initialize shared resources
2. Minimize modules loaded at startup — only install what you use
3. Use the `asm` compiler (default) for better startup performance
4. Set `storeClassFilesOnDisk: true` in `boxlang.json`

---

## Multiple Lambda Functions (URI Routing)

Use the `x-bx-function` header to call a specific method on the class:

```bash
# Calls Lambda.bx run() method (default)
curl https://api.example.com/function

# Calls Lambda.bx processOrder() method
curl -H "x-bx-function: processOrder" https://api.example.com/function
```

In `Lambda.bx`:

```boxlang
class {

    function run( event, context, response ){
        return { message: "Default handler" }
    }

    function processOrder( event, context, response ){
        return orderService.process( event )
    }

    function getUsers( event, context, response ){
        return userService.list( event.limit ?: 20 )
    }

}
```

---

## Local Development with SAM CLI

```bash
# Install AWS SAM CLI
# https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html

# Start local Lambda API
sam local start-api --template workbench/template.yml

# Invoke a specific function locally
sam local invoke BoxLangFunction --event workbench/sampleEvents/test-event.json

# Build the shaded JAR (includes all dependencies)
./gradlew shadowJar
```

---

## Deploying to AWS

```bash
# Configure AWS credentials first
aws configure

# Build, package, and deploy via SAM
./gradlew shadowJar
sam deploy --guided --template workbench/template.yml

# Or use the included deploy scripts
chmod +x workbench/*.sh
./workbench/deploy.sh
```

---

## BoxLang Modules in Lambda

Install modules to the `src/main/resources/boxlang_modules/` directory using CommandBox:

```bash
# Install a module to the Lambda module directory
box install bx-mail --boxlang-home=src/main/resources

# The module will be zipped with the deployment package
```

---

## Production Checklist

- [ ] `Application.bx` initializes shared resources (connections, config) in `onApplicationStart()`
- [ ] `BOXLANG_LAMBDA_DEBUGMODE=false` in production
- [ ] Connection pool size tuned: `BOXLANG_LAMBDA_CONNECTION_POOL_SIZE`
- [ ] Secrets via AWS SSM Parameter Store or Secrets Manager, injected as env vars
- [ ] `boxlang.json` present in deployment package root
- [ ] Lambda timeout set generously for cold starts (30s+ recommended)
- [ ] Memory allocation ≥ 512MB (1024MB+ for better performance)
- [ ] Integration tests passing via `./gradlew test`