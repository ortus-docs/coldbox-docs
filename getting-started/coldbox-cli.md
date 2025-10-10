---
description: >-
  The ColdBox CLI is a powerful command-line interface tool that helps you create, manage, and scaffold ColdBox applications and components with ease.
icon: terminal
---

# 🛠️ ColdBox CLI

Welcome to the **ColdBox CLI** - your ultimate command-line companion for rapid ColdBox development! 🚀 This powerful tool streamlines the creation, management, and scaffolding of ColdBox applications and components for both **CFML** and **BoxLang** projects.

> 🎯 **BoxLang First**: BoxLang is now the **default language** for all new applications, reflecting the modern direction of ColdBox development.

## 📦 CLI Versions & Compatibility

The CLI follows semantic versioning aligned with ColdBox major releases:

| ColdBox Version | CLI Version | Status | Installation Command |
|----------------|-------------|--------|---------------------|
| **ColdBox 8** | `@8` | ✅ **Current** | `box install coldbox-cli@8` |
| **ColdBox 7** | `@7.8.0` | 🟡 **Supported** | `box install coldbox-cli@7.8.0` |
| **ColdBox 6** | `@6` | 🔶 **Legacy** | `box install coldbox-cli@6` |

> 💡 **Pro Tip**: Always use the CLI version that matches your ColdBox framework version for the best compatibility and latest features.

## ⚡ Quick Installation

Get started in seconds with CommandBox:

```bash
# Install the latest ColdBox CLI (v8)
box install coldbox-cli

# Get help anytime
coldbox --help
```

> 🔗 **Source Code**: Explore the CLI on GitHub at [coldbox/coldbox-cli](https://github.com/coldbox/coldbox-cli) - contributions welcome!

## 🎯 Getting Started

The ColdBox CLI supercharges your development workflow with intelligent scaffolding and modern development tools. Whether you're building **BoxLang** or **CFML** applications, the CLI has you covered!

> 📚 **Help is Always Available**: Every command supports the `--help` flag for detailed usage information.

### 🥊 Application Creation

Create stunning ColdBox applications from professionally crafted templates. **BoxLang leads the way** as the default language for modern development:

```bash
# 🎯 Quick Start - Create a BoxLang app (default behavior)
coldbox create app myAwesomeApp

# 🔥 Explicitly choose BoxLang (same as above)
coldbox create app myAwesomeApp --boxlang

# 📜 Create a traditional CFML app
coldbox create app myCFMLApp --cfml
```

#### 🏗️ Application Templates & Features

Choose from various templates and enhance with powerful features:

```bash
# 📋 Modern Templates (Recommended)
coldbox create app myApp skeleton=boxlang   # Pure BoxLang template
coldbox create app myApp skeleton=modern     # Contemporary architecture for CFML or BoxLang

# 🔧 Flat Templates (CFML)
coldbox create app myApp skeleton=rest      # REST API focused
coldbox create app myApp skeleton=flat      # Traditional flat structure
coldbox create app myApp skeleton=supersimple  # Minimal setup
coldbox create app myApp skeleton=vite  # Vite

# ⚡ Power Features
coldbox create app myApp --migrations       # 🗃️ Database migrations
coldbox create app myApp --docker          # 🐳 Container ready
coldbox create app myApp --vite           # 🎨 Modern frontend assets
coldbox create app myApp --rest           # 🌐 REST API configuration

# 🎪 Combine Multiple Features
coldbox create app myFullStackApp --migrations --docker --vite --rest
```

#### 🧙‍♂️ Interactive App Wizard - Perfect for Beginners!

New to ColdBox? The **App Wizard** is your friendly guide to creating the perfect application setup:

```bash
# Launch the interactive wizard
coldbox create app-wizard
```

The wizard walks you through every decision with helpful prompts:

| Step | What It Asks | Why It Matters |
|------|-------------|----------------|
| **📁 Location** | Create in current folder or new directory? | Project organization |
| **🔥 Language** | BoxLang (default) or CFML? | Development preference |
| **🎯 Type** | API/REST service or full web app? | Architecture choice |
| **🎨 Frontend** | Include Vite for modern UI? | Asset management |
| **🐳 Environment** | Docker containerization? | Deployment strategy |
| **🗃️ Database** | Need migration support? | Data management |

**Example Wizard Session**:

```bash
🧙‍♂️ Welcome to the ColdBox App Wizard!

📁 Are you currently inside the "myapp" folder? [y/n]: n
🔥 Is this a BoxLang project? [y/n]: y
🎯 Are you creating an API? [y/n]: n
🎨 Would you like to configure Vite as your Front End UI pipeline? [y/n]: y
🐳 Would you like to setup a Docker environment? [y/n]: y
🗃️ Are you going to require Database Migrations? [y/n]: y

✨ Perfect! Creating your customized ColdBox application...
```

> 💡 **Pro Tip**: The wizard is perfect for exploring all available options and learning about ColdBox features as you create your app!

### 📋 Application Templates - Choose Your Foundation

Select from professionally crafted templates or bring your own via **ForgeBox ID**, **GitHub repo**, **local path**, **ZIP file**, or **URL**. Our modern templates prioritize **BoxLang** for cutting-edge development:

#### 🥊 BoxLang Templates (Recommended for Modern Development)

These can be used for BoxLang PRIME projects or CFML projects with the `bx-cfml-compat` module for compatibility.

| Template | Description | Best For |
|----------|-------------|----------|
| `boxlang` ⭐ | **Default** - Modern ColdBox with latest BoxLang features | New projects, modern architecture |
| `modern` | Contemporary app supporting BoxLang + CFML | Hybrid projects, gradual migration |

#### 📜 FLAT CFML Templates (Traditional Development)

Can also be used for BoxLang projects but using the `bx-cfml-compat` modules to ensure compatibility.

| Template | Description | Best For |
|----------|-------------|----------|
| `flat` | Classic flat-structure ColdBox app | Traditional CFML projects |
| `rest` | BoxLang-optimized REST API template | Microservices, API development |
| `rest-hmvc` | RESTful app with HMVC architecture | Complex API systems |
| `supersimple` | Bare-bones minimal setup | Learning, prototyping |
| `vite` | CFML app with Vite integration _(legacy)_ | Legacy frontend modernization |

#### 🚀 Enhanced Template Features

Modern templates (`boxlang`, `modern`) unlock powerful development features through simple flags:

| Feature Flag | What It Does | Perfect For |
|-------------|-------------|-------------|
| `--vite` 🎨 | Modern frontend with hot reload & optimized builds | Interactive UIs, SPAs |
| `--rest` 🌐 | REST API configuration with OpenAPI docs | Microservices, APIs |
| `--docker` 🐳 | Complete containerization setup | Cloud deployment, consistency |
| `--migrations` 🗃️ | Database migration system | Data-driven apps |

**Power Combinations**:

```bash
# 🎯 Full-Stack Modern App
coldbox create app myApp skeleton=modern --vite --migrations --docker

# 🌐 Production-Ready API
coldbox create app myAPI skeleton=rest --docker --migrations

# 🔥 BoxLang Powerhouse
coldbox create app myApp --vite --rest --docker --migrations
```

#### ⚡ Vite Integration - Modern Frontend Made Easy

Transform your frontend development with **Vite's lightning-fast** build system and hot module replacement:

```bash
# 🔥 Create app with Vite power
coldbox create app myApp --vite

# 🎯 Works with modern templates
coldbox create app myApp skeleton=modern --vite
```

**🎁 What You Get Out of the Box**:

| Feature | Benefit | Developer Experience |
|---------|---------|---------------------|
| 🔧 **Pre-configured Setup** | `vite.config.mjs` ready to go | Zero configuration needed |
| 🔥 **Hot Module Replacement** | Instant updates without refresh | Ultra-fast development |
| 📦 **Optimized Builds** | Code splitting & tree shaking | Production-ready performance |
| 🎨 **Asset Processing** | CSS, SCSS, JS, TS support | Modern toolchain |
| 🔗 **Dev Server + Proxy** | Seamless ColdBox integration | Unified development experience |

**🚀 Development Workflow**:

```bash
# Start development with hot reloading
npm run dev            # ⚡ Lightning fast updates

# Build for production
npm run build          # 📦 Optimized & minified

# Preview production build locally
npm run preview        # 🔍 Test before deploy
```

> 💡 **Pro Tip**: Vite's dev server automatically proxies to your ColdBox application, giving you the best of both worlds!

#### 🐳 Docker Integration - Deploy Anywhere, Run Everywhere

Containerize your ColdBox applications for **consistent development** and **bulletproof deployments**:

```bash
# 🐳 Add Docker superpowers
coldbox create app myApp --docker

# 🚀 Ultimate combo - Docker + everything
coldbox create app myApp --docker --vite --migrations --rest
```

**🎁 Complete Container Solution**:

| Component | What's Included | Benefit |
|-----------|----------------|---------|
| 📦 **Multi-stage Dockerfile** | Optimized for ColdBox apps | Minimal production images |
| 🔧 **docker-compose.yml** | Complete dev environment | One-command setup |
| 🗃️ **Database Services** | PostgreSQL/MySQL ready | Consistent data layer |
| ⚡ **Redis Caching** | High-performance caching | Speed & scalability |
| 🌐 **Environment Config** | Secure variable management | Production-ready security |
| 💚 **Health Checks** | Built-in monitoring | Reliable deployments |

**🚀 Container Commands**:

```bash
# Launch your entire development environment
docker-compose up -d           # 🚀 Start all services

# Monitor your application
docker-compose logs -f app     # 👀 Watch real-time logs

# Clean shutdown
docker-compose down           # 🛑 Stop all services gracefully

# Fresh rebuild
docker-compose up --build     # 🔄 Rebuild & restart
```

> 🎯 **Production Ready**: The Docker setup includes production optimizations like multi-stage builds, security best practices, and health monitoring!

### 🎯 Handlers (Controllers) - Your Application Logic

Generate powerful MVC controllers with intelligent scaffolding for actions, views, and tests:

```bash
# 🎯 Basic handler for common tasks
coldbox create handler UserController

# 🎪 Handler with specific actions
coldbox create handler Users index,show,edit,delete,create

# 🌐 RESTful API handler
coldbox create handler api/Users --rest

# 🔄 Full CRUD resourceful handler
coldbox create handler Photos --resource

# 🎨 Complete package with views and tests
coldbox create handler Users --views --integrationTests
```

**🎁 Handler Generation Options**:

| Flag | What It Creates | Perfect For |
|------|----------------|------------|
| `--rest` | RESTful endpoints (GET, POST, PUT, DELETE) | API development |
| `--resource` | Full CRUD operations | Data management |
| `--views` | Corresponding view templates | Full-stack apps |
| `--integrationTests` | Test files for actions | Quality assurance |

> 💡 **Smart Generation**: The CLI creates handlers in the correct directory structure and generates appropriate code for your project language (BoxLang/CFML)!

### 📊 Models & Services - Your Business Logic Powerhouse

Create robust domain models and business services that form the backbone of your application:

```bash
# 🎯 Simple domain model
coldbox create model User

# 🏗️ Rich model with properties and accessors
coldbox create model User properties=firstName,lastName,email --accessors

# 🗃️ Model with database migration
coldbox create model Product --migration

# 🔧 Model with accompanying service
coldbox create model User --service

# 🎪 The works - model, service, handler, migration, seeder
coldbox create model Customer --all

# ⚙️ Standalone business service
coldbox create service PaymentService
```

**🎁 Model Generation Features**:

| Option | What You Get | Use Case |
|--------|-------------|----------|
| `--accessors` | Getter/setter methods | Clean property access |
| `--migration` | Database table creation | Data persistence |
| `--service` | Business logic layer | Complex operations |
| `--all` | Complete MVC stack | Full feature development |

**💡 Smart Property Generation**:

```bash
# Creates firstName, lastName, email properties with getters/setters
coldbox create model User properties=firstName,lastName,email --accessors
```

> 🚀 **Pro Tip**: Use `--all` when you need a complete feature with model, service, handler, database migration, and test seeders!

### 🎨 Views & Layouts - Beautiful User Interfaces

Craft stunning user interfaces with intelligent view and layout generation:

```bash
# 🎨 Create a view template
coldbox create view users/index

# 🔧 View with helper functions
coldbox create view users/profile --helper

# 📝 View with initial content
coldbox create view welcome content="<h1>Welcome to Our App!</h1>"

# 🖼️ Create layout template
coldbox create layout main

# 🎯 Layout with view rendering
coldbox create layout admin content="<cfoutput>#renderView()#</cfoutput>"
```

**🎁 View Generation Options**:

| Component | Purpose | Best Practice |
|-----------|---------|---------------|
| **Views** | Page content & UI components | Keep focused & reusable |
| **Layouts** | Page structure & common elements | Master templates |
| **Helpers** | View-specific utility functions | Clean separation of concerns |

**💡 Smart Content Generation**:

- Views are placed in the correct directory structure
- Layouts include proper view rendering calls
- Helper files provide utility functions for complex views
- All generated code follows your project's language (BoxLang/CFML)

> 🎨 **Design Tip**: Use layouts for common page structure (header, navigation, footer) and views for specific page content!

### 🔧 Resources & CRUD - Complete Feature Development

Generate complete, production-ready resourceful components with full CRUD operations:

```bash
# 🎯 Single resource (handler, model, views, routes)
coldbox create resource Photos

# 🎪 Multiple resources at once
coldbox create resource Photos,Users,Categories

# 🎨 Custom handler name for better organization
coldbox create resource photos PhotoGalleryController

# 🧪 Production-ready with tests and migrations
coldbox create resource Users --tests --migration
```

**🎁 What Gets Generated**:

| Component | Purpose | Files Created |
|-----------|---------|---------------|
| **🎯 Handler** | CRUD actions (index, show, create, edit, delete) | `handlers/Photos.cfc` |
| **📊 Model** | Data model with validation | `models/Photo.cfc` |
| **🎨 Views** | Complete UI for all actions | `views/photos/*.cfm` |
| **🛣️ Routes** | RESTful URL patterns | Added to `config/Router.cfc` |
| **🧪 Tests** | Unit & integration tests _(optional)_ | `tests/specs/` |
| **🗃️ Migration** | Database table creation _(optional)_ | `resources/database/migrations/` |

**🚀 Generated CRUD Actions**:

```bash
# Your Photos resource creates these endpoints automatically:
GET    /photos          # index()   - List all photos
GET    /photos/new      # new()     - Show create form
POST   /photos          # create()  - Save new photo
GET    /photos/:id      # show()    - Display photo
GET    /photos/:id/edit # edit()    - Show edit form
PUT    /photos/:id      # update()  - Save changes
DELETE /photos/:id      # delete()  - Remove photo
```

> ⚡ **Instant Productivity**: Resources give you a complete feature with working CRUD operations in seconds!

### 📦 Modules - Reusable Application Components

Create powerful, self-contained modules that can be shared across applications or published to ForgeBox:

```bash
# 🎯 Basic module structure
coldbox create module UserManagement

# 🎪 Full-featured module with all components
coldbox create module BlogEngine --models --handlers --views
```

**🎁 Module Architecture**:

Modules are mini-applications within your ColdBox app, complete with their own:

- **📁 Directory Structure** - Organized, self-contained file layout
- **⚙️ ModuleConfig** - Independent configuration and settings
- **🛣️ Routing** - Module-specific URL patterns
- **📦 Dependencies** - Isolated dependency management
- **🔧 Models & Handlers** - Complete MVC architecture _(optional)_
- **🎨 Views & Layouts** - Independent UI components _(optional)_

> 🚀 **Modular Power**: Modules enable microservice architecture, code reuse, and team collaboration on large applications!

### 🧪 Testing - Quality Assurance Made Easy

Generate comprehensive test suites to ensure your application works flawlessly:

```bash
# 🎯 Unit tests for business logic
coldbox create unit models.UserServiceTest

# 📋 BDD specs for behavior testing
coldbox create bdd UserAuthenticationSpecs

# 🌐 Integration tests for full workflows
coldbox create integration-test handlers.UsersTest

# 📊 Model-specific testing
coldbox create model-test User

# 🎪 Interceptor testing with action coverage
coldbox create interceptor-test Security --actions=preProcess,postProcess
```

**🎁 Testing Types & Their Purpose**:

| Test Type | What It Tests | Best For |
|-----------|---------------|----------|
| **🎯 Unit** | Individual methods & functions | Business logic, utilities |
| **📋 BDD** | Behavior & requirements | User stories, acceptance criteria |
| **🌐 Integration** | Component interactions | Workflows, API endpoints |
| **📊 Model** | Data models & validation | Domain logic, persistence |
| **🎪 Interceptor** | AOP & cross-cutting concerns | Security, logging, caching |

**💡 Testing Best Practices**:

- **Fast Feedback** - Unit tests run quickly for immediate validation
- **Behavior Focus** - BDD tests document how features should work
- **Real Scenarios** - Integration tests verify complete user workflows
- **Data Integrity** - Model tests ensure business rules are enforced

> 🚀 **Quality First**: Well-tested applications deploy with confidence and maintain themselves over time!

### 🗄️ ORM & Database

Work with ORM entities and database operations:

```bash
# ORM Entity
coldbox create orm-entity User table=users

# ORM Service
coldbox create orm-service UserService entity=User

# Virtual Entity Service
coldbox create orm-virtual-service UserService

# ORM Event Handler
coldbox create orm-event-handler

# CRUD operations
coldbox create orm-crud User
```

### 🔗 Interceptors

Create AOP interceptors:

```bash
# Basic interceptor
coldbox create interceptor Security

# Interceptor with specific interception points
coldbox create interceptor Logger points=preProcess,postProcess

# With tests
coldbox create interceptor Security --tests
```

### 🔄 Development Workflow

Manage your development environment:

```bash
# Reinitialize ColdBox framework
coldbox reinit

# Auto-reinit on file changes
coldbox watch-reinit

# Open documentation
coldbox docs
coldbox docs search="event handlers"

# Open API documentation
coldbox apidocs
```

### 🎛️ Global Options - Command Superpowers

Enhance any CLI command with these powerful options for a customized experience:

#### 🌟 Universal Command Options

| Option | What It Does | Example Usage |
|--------|-------------|---------------|
| `--force` ⚡ | Overwrite existing files without prompting | `coldbox create handler Users --force` |
| `--open` 📂 | Auto-open generated files in your editor | `coldbox create model User --open` |
| `--help` ❓ | Show detailed command help | `coldbox create app --help` |

#### 🏗️ Application Creation Flags

| Feature Flag | Adds To Your App | Perfect For |
|-------------|------------------|-------------|
| `--migrations` 🗃️ | Database migration system | Data-driven applications |
| `--docker` 🐳 | Complete containerization | Cloud deployments, consistency |
| `--vite` ⚡ | Modern frontend asset pipeline | Interactive UIs, SPAs |
| `--rest` 🌐 | REST API configuration | Microservices, API development |

#### 🔥 Language Control

The CLI automatically detects your project language but you can override when needed:

| Setting | Behavior | When To Use |
|---------|----------|-------------|
| **Default** 🎯 | BoxLang for new apps, auto-detect for existing | Most scenarios |
| `--boxlang` 🥊 | Force BoxLang generation | Override detection |
| `--cfml` 📜 | Force CFML generation | Legacy projects, specific requirements |

**🎪 Power Combinations**:

```bash
# 🚀 Create a modern app with all the bells and whistles
coldbox create app myApp --vite --docker --migrations --rest --open

# 🔧 Force CFML for a legacy project with immediate editing
coldbox create handler Users --cfml --force --open

# 📋 Get detailed help for any command
coldbox create resource --help
```

### 💡 BoxLang Support

The CLI automatically detects BoxLang projects and generates appropriate code. You can also force BoxLang mode using the `--boxlang` flag.

#### 🔍 Automatic Detection

The CLI detects BoxLang projects using three methods (in order of precedence):

1. **Server Engine Detection**: Running on a BoxLang server
2. **TestBox Runner Setting**: When `testbox.runner` is set to `"boxlang"` in `box.json`
3. **Language Property**: When `language` is set to `"boxlang"` in `box.json`

#### ⚙️ Configuration Examples

##### Method 1: Language Property (Recommended)

```json
{
    "name": "My BoxLang App",
    "language": "boxlang",
    "testbox": {
        "runner": "/tests/runner.bxm"
    }
}
```

##### Method 2: TestBox Runner Setting

```json
{
    "name": "My App",
    "testbox": {
        "runner": "boxlang"
    }
}
```

#### 🚀 Usage Examples

```bash
# Default behavior (creates BoxLang code)
coldbox create handler users
coldbox create model User

# Explicit BoxLang generation (usually not needed)
coldbox create handler users --boxlang

# Force CFML generation for legacy projects
coldbox create handler users --cfml
coldbox create app myApp --cfml
```

#### 📝 Generated Code Differences

When BoxLang mode is detected or forced:

- Uses `.bx` file extensions instead of `.cfc`
- Generates `class` syntax instead of `component`
- Uses BoxLang-specific template variants
- Creates BoxLang test files (`.bxm` extensions)

### 🤖 AI Coding Assistance

The CLI now includes **Copilot instructions** to enhance AI-powered development workflows. These instructions help AI assistants understand ColdBox project structure and generate appropriate code:

#### Features

- **Intelligent Code Generation**: AI assistants can better understand ColdBox conventions and patterns
- **Template-Aware Suggestions**: Context-aware code suggestions based on your project type
- **BoxLang & CFML Support**: Appropriate suggestions for both language targets
- **Framework Integration**: Deep understanding of ColdBox architecture and best practices

#### Copilot Instructions

The CLI includes specialized instruction sets:

- **Modern Apps**: Instructions optimized for contemporary ColdBox applications
- **Legacy Projects**: Support for traditional flat-structure applications
- **BoxLang Focus**: Enhanced support for BoxLang-specific patterns
- **Framework Patterns**: MVC, HMVC, and REST API architectural guidance

These instructions are automatically included in modern application templates to provide the best AI coding experience out of the box.

### 📖 Getting Help - Never Get Stuck

The ColdBox CLI has your back with comprehensive help at every level:

#### 🎯 Quick Help Commands

```bash
# 📋 See all available commands
coldbox help

# 🔍 Get detailed help for any command
coldbox create handler --help
coldbox create model --help
coldbox create app --help

# 📊 Check your CLI version
coldbox --version
```

#### 🆘 When You Need Help

| Situation | Command | What You Get |
|-----------|---------|-------------|
| **🤔 What can I do?** | `coldbox help` | Full command list |
| **📋 How do I use this command?** | `coldbox create app --help` | Detailed usage & examples |
| **🐛 Something's not working** | `coldbox --version` | Version info for troubleshooting |
| **📚 I want to learn more** | Check the [CLI Repository](https://github.com/coldbox/coldbox-cli) | Source code & documentation |

#### 💡 Pro Help Tips

- Every command has `--help` - never hesitate to use it!
- Help shows examples, options, and flags for each command
- When reporting issues, always include your CLI version
- The GitHub repository has extensive documentation and examples

> 🎓 **Learning Path**: Start with `coldbox create app-wizard` to explore all options interactively, then use specific commands as you become more comfortable!
