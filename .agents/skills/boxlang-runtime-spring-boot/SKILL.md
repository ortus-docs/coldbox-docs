---
name: boxlang-runtime-spring-boot
description: "Use this skill when integrating BoxLang with Spring Boot applications, including the starter dependency, MVC view resolution, template structure, accessing Spring Model attributes, configuration via application.properties, and returning BoxLang views from Spring controllers."
---

# BoxLang with Spring Boot

## Overview

The `boxlang-spring-boot-starter` automatically configures BoxLang as a view
engine inside any Spring Boot 3.x application. Spring controllers return logical
view names, which Spring resolves to `.bxm` template files. Spring `Model`
attributes are available directly in templates via the BoxLang `variables` scope.

**Requirements:** Spring Boot 3.x · BoxLang 1.11.0+ · JDK 21+

---

## Dependency

### Gradle

```groovy
dependencies {
    implementation "io.boxlang:boxlang-spring-boot-starter:1.0.0"
}
```

### Maven

```xml
<dependency>
    <groupId>io.boxlang</groupId>
    <artifactId>boxlang-spring-boot-starter</artifactId>
    <version>1.0.0</version>
</dependency>
```

---

## Zero-Config Setup

Adding the dependency activates `BoxLangAutoConfiguration`, which registers:

- `BoxLangViewResolver` — resolves view names to `classpath:/templates/<name>.bxm`
- BoxLang runtime singleton (`BoxRuntime.getInstance()`)

No additional configuration is required to get started.

---

## Spring Controller

Return a logical view name (without path or extension) from a controller method:

```java
@Controller
public class HomeController {

    @GetMapping("/")
    public String home( Model model ){
        model.addAttribute( "title",   "Welcome" );
        model.addAttribute( "message", "Hello from BoxLang!" );
        return "home";   // → classpath:/templates/home.bxm
    }

    @GetMapping("/users")
    public String users( Model model ){
        model.addAttribute( "users", userRepository.findAll() );
        return "users";  // → classpath:/templates/users.bxm
    }
}
```

---

## Template Structure

Templates live in `src/main/resources/templates/` and use the `.bxm` extension:

```
src/
└── main/
    ├── java/           ← Spring controllers, services
    └── resources/
        ├── application.properties
        └── templates/
            ├── home.bxm
            ├── users.bxm
            └── partials/
                └── header.bxm
```

---

## `.bxm` Template — Accessing Model Attributes

Spring `Model` attributes are available directly in the BoxLang `variables` scope:

```html
<!-- src/main/resources/templates/home.bxm -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title><bx:output>#encodeForHTML( title )#</bx:output></title>
</head>
<body>
    <h1><bx:output>#encodeForHTML( title )#</bx:output></h1>
    <p><bx:output>#encodeForHTML( message )#</bx:output></p>
</body>
</html>
```

All three forms are equivalent:

```html
<bx:output>#title#</bx:output>
<bx:output>#variables.title#</bx:output>
<!--- or use script inside a bx:script block --->
```

---

## Iterating Collections

```html
<!-- templates/users.bxm -->
<bx:output>
<ul>
    <bx:loop array="#users#" item="user">
        <li>#encodeForHTML( user.name )# — #encodeForHTML( user.email )#</li>
    </bx:loop>
</ul>
</bx:output>
```

---

## Conditional Rendering

```html
<bx:if condition="#isLoggedIn#">
    <p>Welcome back, <bx:output>#encodeForHTML( currentUser.name )#</bx:output>!</p>
<bx:else>
    <p><a href="/login">Log in</a></p>
</bx:if>
```

---

## Including Partials

```html
<!DOCTYPE html>
<html>
<head>
    <bx:include template="/templates/partials/head.bxm">
</head>
<body>
    <bx:include template="/templates/partials/nav.bxm">
    <main>
        <!-- page content here -->
    </main>
</body>
</html>
```

---

## Configuration (application.properties)

BoxLang settings are namespaced under `boxlang.*`:

```properties
# Template prefix/suffix (defaults shown)
boxlang.webserver.templatesPath=classpath:/templates/
boxlang.webserver.templatesSuffix=.bxm

# BoxLang home directory
boxlang.home=/opt/boxlang

# Log level
boxlang.logging.level=INFO

# View resolver order (lower = higher priority; coexist with Thymeleaf etc.)
boxlang.viewResolver.order=1
```

---

## Coexisting with Thymeleaf / FreeMarker

`BoxLangViewResolver` participates in Spring's standard resolver chain. Configure
the resolver `order` to establish priority:

```properties
# BoxLang handles .bxm; Thymeleaf handles .html
boxlang.viewResolver.order=1
spring.thymeleaf.order=2
```

---

## Direct Runtime Access from Java

You can access the BoxLang runtime directly from any Spring bean:

```java
import ortus.boxlang.runtime.BoxRuntime;
import ortus.boxlang.runtime.scopes.Key;

@Service
public class BoxLangService {

    private final BoxRuntime boxlang = BoxRuntime.getInstance();

    public String renderSnippet(){
        // Execute an inline BoxLang expression
        return boxlang.executeStatement( "now().format('yyyy-MM-dd')" );
    }
}
```

---

## Checklist

- [ ] `boxlang-spring-boot-starter` on the classpath
- [ ] Spring Boot 3.x and JDK 21+ in use
- [ ] Templates in `src/main/resources/templates/` with `.bxm` extension
- [ ] Controllers return logical view names (no path prefix, no extension)
- [ ] Model attributes encoded with `encodeForHTML()` before output
- [ ] Resolver order configured when coexisting with other view technologies
- [ ] Secrets and sensitive config in `application.properties` via environment variable placeholders, not hard-coded