---
description: Discover the power of ColdBox 8.0.0
---

# What's New With 8.0.0

🎉 **Welcome to ColdBox 8.0.0** - the most powerful and feature-rich release of the ColdBox HMVC platform yet! This major release brings exciting new capabilities, performance improvements, and modern development features that will supercharge your CFML applications.

## 🚀 Major Highlights

ColdBox 8.0.0 introduces groundbreaking features like **BoxLang Prime** integration, **Virtual Thread Executors**, enhanced **AI-powered error handling**, and a completely revamped developer experience. Whether you're building REST APIs, full-stack web applications, or microservices, this release has something exciting for every CFML developer.

### 🎯 Key Features At-a-Glance

- 🔥 **BoxLang Prime** - Native BoxLang module with BoxLang compilation and optimization (`bx-coldbox`)
- ⚡ **Virtual Thread Executors** - Modern async programming support
- 🤖 **AI-Enhanced Whoops Experience** - Intelligent error diagnostics
- 🎨 **Enhanced Developer Tools** - Updated CLI, VSCode extension, and templates
- 🔧 **CacheBox Updates** - Better performance and BoxLang integration
- 🌐 **Modern Templates** - New BoxLang and Modern application templates
- 🧹 **Legacy Cleanup** - Removal of deprecated features for a cleaner codebase

## 🎯 Engine Support

ColdBox 8.0.0 supports the following CFML engines:

- ⚡ **BoxLang 1.0.0+** (with native compilation support!)
- 📦 **Adobe ColdFusion 2023+**
- 🔥 **Lucee 5+**

> 🚨 **Important:** Adobe ColdFusion 2021 support has been **dropped** in this release. Please upgrade to CF 2023 or newer for continued support.

### 📚 ColdBox Documentation MCP Server

ColdBox documentation is now accessible via the **Model Context Protocol (MCP)**! Connect AI assistants like Claude, GitHub Copilot, and other MCP-compatible tools directly to the comprehensive ColdBox documentation for instant access to language references, framework features, and best practices.

**Connect to the MCP Server:**

* `https://coldbox.ortusbooks.com/~gitbook/mcp`
* `https://cachebox.ortusbooks.com/~gitbook/mcp`
* `https://logbox.ortusbooks.com/~gitbook/mcp`
* `https://wirebox.ortusbooks.com/~gitbook/mcp`

**VSCode Connect:**

* [ColdBox](vscode:mcp/install?%7B%22name%22%3A%22ColdBox%20HMVC%20Documentation%22%2C%22url%22%3A%22https%3A%2F%2Fcoldbox.ortusbooks.com%2F~gitbook%2Fmcp%22%7D)
* [CacheBox](vscode:mcp/install?%7B%22name%22%3A%22CacheBox%3A%20Enterprise%20Caching%22%2C%22url%22%3A%22https%3A%2F%2Fcachebox.ortusbooks.com%2F~gitbook%2Fmcp%22%7D)
* [LogBox](vscode:mcp/install?%7B%22name%22%3A%22LogBox%20%3A%20Enterprise%20Logging%20%26%20Messaging%22%2C%22url%22%3A%22https%3A%2F%2Flogbox.ortusbooks.com%2F~gitbook%2Fmcp%22%7D)
* [WireBox](vscode:mcp/install?%7B%22name%22%3A%22WireBox%20%3A%20Dependency%20Injection%20%26%20AOP%22%2C%22url%22%3A%22https%3A%2F%2Fwirebox.ortusbooks.com%2F~gitbook%2Fmcp%22%7D)

This enables developers to:

* 🔍 Search ColdBox documentation semantically from within AI assistants
* 💡 Get instant answers about BIFs, components, and framework features
* 📖 Access code examples and best practices without leaving your workflow
* 🤖 Enhance AI-assisted ColdBox development with authoritative documentation

## 🔥 BX-ColdBox - BoxLang Native Edition

In this release, we now introduce a new version of ColdBox specifically optimized and pre-compiled for BoxLang.  Introducing `bx-coldbox` which you can install via CommandBox or the BoxLang module installer:

```bash
# CommandBox
box install bx-coldbox

# BoxLang Module
install-bx-module bx-coldbox
```

This version of ColdBox is pre-compiled with BoxLang and optimized for performance.  It also includes the new BoxLang Prime features for CacheBox, LogBox, and WireBox.  It installs into the engine itself instead of your application.  This means, you can have ColdBox available to any BoxLang application without needing to install it in each app.  It also means that you can leverage the native BoxLang compilation for better performance, startup times, and security.


## ❤️‍🔥 ColdBox BoxLang PRIME

Say hello to **BoxLang PRIME** — the next evolution of the ColdBox Platform. For the first time ever, you can build and run your ColdBox applications natively in BoxLang — no CFML Compatibility Module required!

BoxLang PRIME delivers full native integration across **CacheBox, LogBox, and WireBox**, unlocking a new era of speed, security, and seamless performance.

- 🔥 Experience faster startups.
- ⚡ Harness native BoxLang compilation.
- 🧠 Build smarter, lighter, and more future-ready ColdBox apps.

Combined with the new `bx-coldbox` package, BoxLang PRIME is the ultimate way to build modern JVM applications.

**Please Note: Not all modules are yet BoxLang PRIME compatible.** We are working hard to bring you full compatibility across the entire ColdBox ecosystem. Stay tuned for updates!

## 💻 ColdBox Desktop Applications

We’ve been hard at work on something truly game-changing: soon, **you’ll be able to run your ColdBox applications as fully functional desktop apps with BoxLang Desktop.**

That’s right — build cross-platform desktop experiences powered by ColdBox and BoxLang, all with the same productivity and elegance you already love.  With the introduction of `bx-coldbox` and ColdBox BoxLang PRIME support, you can now leverage the full power of ColdBox in desktop environments.

- 🖥️ One language.
- 🌍 Any platform.
- ⚡ Endless possibilities.

Stay tuned, this is coming real soon.

## 🛠️ ColdBox CLI - Enhanced Developer Experience

```
 ██████╗ ██████╗ ██╗     ██████╗ ██████╗  ██████╗ ██╗  ██╗      ██████╗██╗     ██╗
██╔════╝██╔═══██╗██║     ██╔══██╗██╔══██╗██╔═══██╗╚██╗██╔╝     ██╔════╝██║     ██║
██║     ██║   ██║██║     ██║  ██║██████╔╝██║   ██║ ╚███╔╝█████╗██║     ██║     ██║
██║     ██║   ██║██║     ██║  ██║██╔══██╗██║   ██║ ██╔██╗╚════╝██║     ██║     ██║
╚██████╗╚██████╔╝███████╗██████╔╝██████╔╝╚██████╔╝██╔╝ ██╗     ╚██████╗███████╗██║
 ╚═════╝ ╚═════╝ ╚══════╝╚═════╝ ╚═════╝  ╚═════╝ ╚═╝  ╚═╝      ╚═════╝╚══════╝╚═╝
```

The ColdBox CLI has been **completely revamped** 🎨 to support ColdBox 8 applications with enhanced features:

- 🆕 **Migration Creation** - Generate database migrations effortlessly
- 🐳 **Docker Support** - Streamlined Docker integration for your applications
- ⚡ **Vite Support** - Modern frontend tooling integration
- 🧪 **API Testing Tools** - Built-in testing capabilities
- 📋 **Project Scaffolding** - Quick application setup
- 🔧 **Enhanced Generators** - More code generation options
- 🤖 **AI Integration** - Leverage AI for smarter code suggestions
- 📚 **Documentation** - It has also been completely documented here: [ColdBox CLI Docs](../../getting-started/coldbox-cli.md).

Install it via CommandBox:

```bash
install coldbox-cli
coldbox --help
```

What's also powerful is that using our new application templates the CLI can now configure your application for:

- Docker Creation and support
- Vite support for modern frontend development
- Maven support for Java dependencies
- REST Only applications
- Much More

## 🤖 AI First

All application templates now come with AI assistance via our VSCode BoxLang extension and copilot instructions.  You can get code suggestions, event handler code generation, and more.  Just install the BoxLang VSCode Extension and start coding: https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-boxlang-developer-pack

All of our modules, and ColdBox core have been updated with copilot instructions to help you get the most out of your AI assistants.

## 🥊 New Application Layout Templates

We have been working on these new layouts and experimenting with them for over 2 years now.  We are excited to announce that they are now **production ready** and available for you to use.  These new layouts embrace modern development practices, non-root based architectures, and of course BoxLang.

They are more intuitive, easier to understand, and provide a better developer experience than the traditional flat put everything in the webroot approach.  They also embrace modern frontend tooling like Vite, TailwindCSS, and more.  Icing on the cake is that now if you are using BoxLang, we include a `Build.bx` that will allow you to build, validate, pre-compile and package your application for deployment.

We will also be introducing soon the new `coldbox deploy` command that will allow you to deploy your application to any Ortus Deployable Server or any CommandBox server.  Stay tuned!

Here are our new default templates:

- 🔥 **BoxLang Template** - Native BoxLang ColdBox applications https://github.com/coldbox-templates/boxlang
- 🎨 **Modern Template** - Non-root based architecture (now operational!) https://github.com/coldbox-templates/modern

```bash
# Use the new boxlang template
coldbox create app MyBoxLangApp

# Use the new modern template
coldbox create app MyBoxLangApp skeleton=modern
coldbox create app MyBoxLangApp --cfml
```

### 🔥 BoxLang Template - The Future is Here

**Pure BoxLang Power** - Experience the first **native BoxLang ColdBox template** designed from the ground up for modern JVM applications!

**What You Get:**

- 🚀 **Native BoxLang compilation** with `Build.bx` for lightning-fast deployments
- 🎯 **Zero-config Docker support** with optimized runtime containers
- ⚡ **Vite integration** for modern frontend development with hot-reload
- 🧪 **Built-in testing suite** with TestBox integration and CI/CD workflows
- 🤖 **AI-first development** with VSCode BoxLang extension and copilot instructions
- 📦 **Maven support** for Java dependencies and enterprise builds
- 🛡️ **Security by design** with non-root architecture and DevContainer support
- 🎨 **REST-ready** with dedicated API structure and OpenAPI documentation

**Perfect for:** Greenfield projects, microservices, desktop applications, and teams ready to embrace the future of JVM development.

```
MyBoxLangApp/
├── 📁 app/                    # 🔒 Secure ColdBox App
│   ├── config/               # Configuration
│   ├── handlers/             # Event handlers (controllers)
│   ├── interceptors/         # Interceptors
│   ├── layouts/              # Layout templates
│   ├── models/               # Business logic layer
│   ├── modules/               # Custom Modules
│   ├── views/                # Presentation templates
│   └── layouts/              # Page layout templates
├── 📁 modules/                # 🗳️ CommandBox Tracked Modules
├── 📁 public/                # 🌐 Web-accessible directory
│   ├── index.bxm             # Application entry point
│   ├── includes/             # CSS, JS, images
│   └── Application.bx        # Public bootstrap
├── 📁 resources/             # 🛠️ Development assets
│   └── database/migrations/  # Database version control
├── 📁 runtime/               # 🏃 Runtime libraries & logs
├── 📁 tests/                 # 🧪 Testing suite
└── Build.bx                  # 🚀 BoxLang build automation
└── box.json                  # CommandBox project descriptor
└── server.json                  # CommandBox server descriptor
└── pom.xml                   # Maven project descriptor
```

**🔥 Key Architecture Benefits:**

- **Security First**: Application code is completely isolated from web access
- **Modern Tooling**: Vite for frontend, Maven for dependencies, Docker for deployment
- **BoxLang Native**: Pure `.bx` and `.bxm` files with native compilation support
- **Zero Configuration**: Everything works out of the box with sensible defaults

### 🎨 Modern Template - Enterprise-Grade Architecture

**Production-Ready Excellence** - Built for teams who demand **enterprise-level structure** without sacrificing developer productivity. This template brings years of production experience into a clean, maintainable architecture.

**What You Get:**

- 🏗️ **Non-root security architecture** keeping your application safe from web-based attacks
- 🐳 **Complete Docker ecosystem** with multi-stage builds and production optimization
- 🔄 **Database migration support** with CF Migrations for seamless deployments
- 📊 **Comprehensive monitoring** with structured logging and application metrics
- 🧩 **Modular design** with clear separation of concerns across all layers
- 🛠️ **Enterprise tooling** with CF Config, formatting, and linting pre-configured
- 🚀 **CI/CD ready** with GitHub Actions for testing, building, and deployment
- 📱 **Frontend flexibility** supporting both traditional CFML views and modern JS frameworks

**Perfect for:** Enterprise applications, legacy modernization, teams requiring strict security compliance, and applications with complex deployment requirements.

```
MyModernApp/
├── 📁 app/                    # 🔒 Secure ColdBox Application (above webroot)
│   ├── config/               # Configuration
│   ├── handlers/             # Event handlers (controllers)
│   ├── interceptors/         # Interceptors
│   ├── layouts/              # Layout templates
│   ├── models/               # Business logic layer
│   ├── modules/               # Custom Modules
│   ├── views/                # Presentation templates
│   └── layouts/              # Page layout templates
├── 📁 public/                # 🌐 Web-accessible directory
│   ├── index.cfm             # Application entry point
│   └── includes/             # Static assets (CSS, JS, images)
├── 📁 docker/                # 🐳 Container orchestration
│   ├── Dockerfile            # Multi-stage production builds
│   └── docker-compose.yml    # Development environment
├── 📁 resources/             # 🗃️ Application resources
│   └── database/migrations/  # Database version control
├── 📁 lib/                   # 📚 Framework libraries
│   ├── coldbox/              # ColdBox framework files
│   └── testbox/              # Testing framework
└── 📁 tests/                 # 🧪 Comprehensive test suite
└── box.json                  # CommandBox project descriptor
└── server.json                  # CommandBox server descriptor
└── pom.xml                   # Maven project descriptor
```

**🎯 Enterprise Architecture Benefits:**

- **Separation of Concerns**: Clean MVC with modular HMVC capabilities
- **Database Migrations**: Version-controlled schema changes with CF Migrations
- **Docker Ready**: Production-optimized containers with development compose
- **Enterprise Tooling**: CF Config, linting, formatting, and CI/CD integration

## AI Powered Whoops!

We have completely revamped the ColdBox Whoops experience to include AI capabilities, enhanced stack traces, improved UI and so much more.  This means that when an error happens, you will get not only the stack trace, but also AI generated suggestions on how to fix it, links to documentation, and more.

## ⚡ Major New Features & Enhancements

### 🔥 Virtual Thread Executors Support

ColdBox 8.0.0 introduces native support for **Virtual Thread Executors**, bringing modern asynchronous programming capabilities to your CFML applications. Build high-performance, non-blocking applications with ease.

### 🤖 AI-Enhanced Whoops Experience

Experience the future of debugging with our **AI-powered error handling**. Get intelligent suggestions, context-aware debugging tips, and smarter error diagnosis to solve issues faster than ever.

### 🏗️ WireBox Enhancements

- **🎯 Delegates** - Reusable component behaviors for cleaner code
- **⏱️ Lazy Properties** - On-demand property initialization for better performance
- **👀 Property Observers** - React to property changes automatically
- **💾 Transient Request Cache** - Optimized request-scoped caching

### 📋 Improved Logging Experience

- **🎨 Pretty JSON Output** - Beautiful, readable JSON in your logs
- **🔒 Closure-based Logging** - Performance-optimized logging with lambda functions
- **🔧 Better Exception Handling** - Enhanced error serialization and reporting

### 🗄️ CacheBox & BoxLang Prime

Native **BoxLang Prime** integration across the entire platform, bringing:

- **⚡ Enhanced Performance** - Native compilation benefits
- **🔧 Better Integration** - Seamless BoxLang ecosystem support
- **📦 Optimized Distribution** - Engine-level installation options

### 🎯 Developer Experience Improvements

- **📅 Enhanced Scheduled Tasks** - Better task management and scheduling
- **🛣️ Improved Routing** - Enhanced path handling and URL generation
- **🧪 Testing Enhancements** - Better testing tools and utilities
- **🎨 View Improvements** - Enhanced rendering capabilities

### 🧹 Legacy Cleanup

ColdBox 8.0.0 removes deprecated features to keep the codebase modern and maintainable:

- **Removed Client Flash** - Legacy insecure storage method
- **Updated BeanPopulator** - Replaced with ObjectPopulator
- **Router Modernization** - Removed deprecated routing methods
- **Environment Variable Handling** - Enhanced Env delegate support

----

# 🎶 Release Notes

## ColdBox Core

### New Feature

[COLDBOX-1332](https://ortussolutions.atlassian.net/browse/COLDBOX-1332) Virtual Thread executors support

[COLDBOX-1333](https://ortussolutions.atlassian.net/browse/COLDBOX-1333) Make the build Executor public, for devs that just want executors built and NOT registered

[COLDBOX-1334](https://ortussolutions.atlassian.net/browse/COLDBOX-1334) Executors more stats for issue detection

[COLDBOX-1341](https://ortussolutions.atlassian.net/browse/COLDBOX-1341) New Whoops Experience with AI Capabilities

### Improvement

[COLDBOX-1331](https://ortussolutions.atlassian.net/browse/COLDBOX-1331) Add ability to ignore or include RC keys when caching events

[COLDBOX-1343](https://ortussolutions.atlassian.net/browse/COLDBOX-1343) Incorporate appName into lock names to give better uniqueness

[COLDBOX-1360](https://ortussolutions.atlassian.net/browse/COLDBOX-1360) Bootstrap was not returning false or true on the right location for onRequestStart

### Bugs

[COLDBOX-1328](https://ortussolutions.atlassian.net/browse/COLDBOX-1328) Improve error message for Module Settings in ColdBox DSL

[COLDBOX-1329](https://ortussolutions.atlassian.net/browse/COLDBOX-1329) Module Config override convention depends on file system case sensitivity

[COLDBOX-1330](https://ortussolutions.atlassian.net/browse/COLDBOX-1330) missing replacement of double // on layouts/views on new rendering schemas

[COLDBOX-1338](https://ortussolutions.atlassian.net/browse/COLDBOX-1338) RequestContext StatusCode Should Default to 200

[COLDBOX-1340](https://ortussolutions.atlassian.net/browse/COLDBOX-1340) ColdboxProxy throws "invalid call of the function ReplaceNoCase"

[COLDBOX-1345](https://ortussolutions.atlassian.net/browse/COLDBOX-1345) NotAuthorized catch in RestHandler should return onAuthorizationFailure - not onAuthenticationFailure

[COLDBOX-1350](https://ortussolutions.atlassian.net/browse/COLDBOX-1350) DateTimeHelper timeUnitToSeconds needs to ignore converting seconds if chosen

[COLDBOX-1359](https://ortussolutions.atlassian.net/browse/COLDBOX-1359) Regression view layout relative app mapping paths

[COLDBOX-1361](https://ortussolutions.atlassian.net/browse/COLDBOX-1361) Routing service was not evaluating the coldbox web mapping when public facing app is in another folder than the coldbox app

### Tasks

[COLDBOX-1346](https://ortussolutions.atlassian.net/browse/COLDBOX-1346) Client Flash removal as legacy

[COLDBOX-1352](https://ortussolutions.atlassian.net/browse/COLDBOX-1352) BeanPopulator finally removed

[COLDBOX-1353](https://ortussolutions.atlassian.net/browse/COLDBOX-1353) Util: getSystemSetting, getSystemProperty, getEnv\(\) removed in favor to the Env Delegate

[COLDBOX-1354](https://ortussolutions.atlassian.net/browse/COLDBOX-1354) RequestContext removed methods: isSES\(\), setSESEnabled\(\)

[COLDBOX-1355](https://ortussolutions.atlassian.net/browse/COLDBOX-1355) Remove ColdBox 4 compat : getModulesRoutingTable\(\), includeRoutes\(\) in the Router.cfc

[COLDBOX-1356](https://ortussolutions.atlassian.net/browse/COLDBOX-1356) Removeal of Router.with\(\) and endWith\(\) in favor of the group\(\) closures.

[COLDBOX-1357](https://ortussolutions.atlassian.net/browse/COLDBOX-1357) Router addRoute\(\) matchVariables argument finally removed

[COLDBOX-1358](https://ortussolutions.atlassian.net/browse/COLDBOX-1358) ProcessState in the InterceptorService is now finally removed

## CacheBox

### New Features

[CACHEBOX-91](https://ortussolutions.atlassian.net/browse/CACHEBOX-91) BoxLang Prime

### Bugs

[CACHEBOX-90](https://ortussolutions.atlassian.net/browse/CACHEBOX-90) CacheBox CFProvider Only Works with EhCache

## LogBox

### New Features

[LOGBOX-84](https://ortussolutions.atlassian.net/browse/LOGBOX-84) BoxLang Prime

### Improvements

[LOGBOX-83](https://ortussolutions.atlassian.net/browse/LOGBOX-83) Add new LogBox configuration option to disallow serializing complex objects: serializeExtraInfo

## WireBox

### New Features

[WIREBOX-156](https://ortussolutions.atlassian.net/browse/WIREBOX-156) BoxLang prime

### Tasks

[WIREBOX-157](https://ortussolutions.atlassian.net/browse/WIREBOX-157) getCacheBoxConfig\(\) on WireBox Binder removed after deprecation

[WIREBOX-158](https://ortussolutions.atlassian.net/browse/WIREBOX-158) Binder.getProperty\(\) \`default\` Argument Removed