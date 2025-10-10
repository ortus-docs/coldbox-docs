---
description: Discover the power of ColdBox 8.0.0
---

# What's New With 8.0.0

🎉 **Welcome to ColdBox 8.0.0** - the most powerful and feature-rich release of the ColdBox HMVC platform yet! This major release brings exciting new capabilities, performance improvements, and modern development features that will supercharge your CFML applications.

## 🚀 Major Highlights

ColdBox 8.0.0 introduces groundbreaking features like **BoxLang Prime** integration, **Virtual Thread Executors**, enhanced **AI-powered error handling**, and a completely revamped developer experience. Whether you're building REST APIs, full-stack web applications, or microservices, this release has something exciting for every CFML developer.

### 🎯 Key Features At-a-Glance

- 🔥 **BoxLang Prime** - Native BoxLang module with BoxLang compilation and optimization
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

## 🔥 BX-ColdBox - BoxLang Native Edition

In this release, we now introduce a new version of ColdBox specifically optimized and compiled for BoxLang.  Introducing `bx-coldbox` which you can install via CommandBox or the BoxLang module installer:

```bash
# CommandBox
box install bx-coldbox

# BoxLang Module
install-bx-module bx-coldbox
```

This version of ColdBox is compiled with BoxLang and optimized for performance.  It also includes the new BoxLang Prime features for CacheBox, LogBox, and WireBox.  It installs into the engine itself instead of your application.

## 🛠️ ColdBox CLI - Enhanced Developer Experience

```
 ██████╗ ██████╗ ██╗     ██████╗ ██████╗  ██████╗ ██╗  ██╗      ██████╗██╗     ██╗
██╔════╝██╔═══██╗██║     ██╔══██╗██╔══██╗██╔═══██╗╚██╗██╔╝     ██╔════╝██║     ██║
██║     ██║   ██║██║     ██║  ██║██████╔╝██║   ██║ ╚███╔╝█████╗██║     ██║     ██║
██║     ██║   ██║██║     ██║  ██║██╔══██╗██║   ██║ ██╔██╗╚════╝██║     ██║     ██║
╚██████╗╚██████╔╝███████╗██████╔╝██████╔╝╚██████╔╝██╔╝ ██╗     ╚██████╗███████╗██║
 ╚═════╝ ╚═════╝ ╚══════╝╚═════╝ ╚═════╝  ╚═════╝ ╚═╝  ╚═╝      ╚═════╝╚══════╝╚═╝
```

The ColdBox CLI has been **completely revamped** 🎨 to support both ColdBox 7 and 8 applications with enhanced features:

- 🆕 **Migration Creation** - Generate database migrations effortlessly
- 🧪 **API Testing Tools** - Built-in testing capabilities
- 📋 **Project Scaffolding** - Quick application setup
- 🔧 **Enhanced Generators** - More code generation options

Install it via CommandBox:

```bash
install coldbox-cli
coldbox --help
```

It has also been completely documented in it's readme: [https://github.com/coldbox/coldbox-cli](https://github.com/coldbox/coldbox-cli)

## 💻 VSCode ColdBox Extension - Enhanced IDE Support

![VSCode ColdBox Extension](../../.gitbook/assets/image%20(6).png)

Our [VSCode ColdBox extension](https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-coldbox) has been updated with exciting new features:

- ✨ **Latest Snippets** - Updated code snippets for ColdBox 8
- 🏗️ **Enhanced Skeletons** - Improved code generation templates
- 🔧 **Better IntelliSense** - Smarter code completion
- 📋 **Project Templates** - Quick project scaffolding from within VSCode

{% embed url="https://marketplace.visualstudio.com/items?itemName=ortus-solutions.vscode-coldbox" %}

## 📋 Application Templates - Modern Development

All application templates have been **completely updated** to support our LTS strategy and modern development practices. We now have templates for each major ColdBox iteration, plus exciting new additions:

### 🆕 New Templates
- 🔥 **BoxLang Template** - Native BoxLang ColdBox applications
- 🎨 **Modern Template** - Non-root based architecture (now operational!)

### 📦 Template Portfolio

{% @github-files/github-code-block url="https://github.com/coldbox-templates" %}

Here is a listing of the latest supported templates:

| Template | Slug | Description |
|----------|------|-------------|
| BoxLang | `boxlang` | A ColdBox application template for BoxLang applications |
| Default | `default` | A flat and simple ColdBox application template |
| Elixir | `elixir` | A template that leverages ColdBox Elixir for asset management |
| Modern | `modern` | A fresh new approach to ColdBox applications that are non-root based |
| Rest | `rest` | A base REST API using ColdBox |
| Rest HMVC | `rest-hmvc` | An HMVC REST API using modules |
| Super Simple | `supersimple` | Barebones conventions baby! |

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

# Release Notes

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