---
name: boxlang-templating
description: "Use this skill when writing BoxLang markup templates (.bxm files), mixing HTML with BoxLang output expressions, using template components like bx:output, bx:loop, bx:if, bx:include, bx:script, building views, or creating any HTML-generating template files."
---

# BoxLang Templating Language

## Overview

BoxLang's templating language uses `.bxm` files (BoxLang Markup) to mix HTML with
dynamic server-side code. The default mode in `.bxm` files is HTML output — you
embed BoxLang logic using `<bx:*>` tags and output expressions with `#expression#`.

---

## File Types Recap

| Extension | Default mode | Primary use |
|-----------|-------------|-------------|
| `.bx` | Script | Classes, services, models |
| `.bxs` | Script | Standalone scripts, CLI tools |
| `.bxm` | Tag/markup | Templates, views, HTML pages |

---

## Output Expression Syntax

Use `#expression#` to interpolate values. The expression is evaluated and output
is HTML-escaped automatically inside `<bx:output>`:

```html
<bx:output>
    <h1>Hello, #user.name#!</h1>
    <p>Today is #dateFormat( now(), "long" )#</p>
    <p>Items: #order.items.len()#</p>
</bx:output>
```

**Always wrap output in `<bx:output>`** — without it, `#variable#` is treated
as a literal string and not evaluated:

```html
<!-- BAD: #name# is printed as literal text, not evaluated -->
<p>#name#</p>

<!-- GOOD: #name# is evaluated and output -->
<bx:output><p>#name#</p></bx:output>
```

---

## Core Template Components

### `<bx:output>` — Render Dynamic Content

```html
<bx:output>
    <p>User: #user.firstName# #user.lastName#</p>
    <p>Email: #encodeForHTML( user.email )#</p>
</bx:output>
```

Options:
- `encodeFor="html"` — auto-encode all expressions (recommended for untrusted data)

```html
<!-- Auto-encode all output in this block -->
<bx:output encodeFor="html">
    <p>#userSuppliedContent#</p>
</bx:output>
```

### `<bx:set>` — Declare Variables

```html
<bx:set var="greeting" value="Hello, #user.name#!">
<bx:set var="items" value="#productService.getFeatured()#">
<bx:set var="count" value="#items.len()#">
```

### `<bx:if>` / `<bx:elseif>` / `<bx:else>` — Conditionals

```html
<bx:if condition="#user.isLoggedIn#">
    <p>Welcome back, #encodeForHTML( user.name )#!</p>
<bx:elseif condition="#session.hasGuideToken#">
    <p>Continue as guest</p>
<bx:else>
    <a href="/login">Please log in</a>
</bx:if>
```

### `<bx:loop>` — Iteration

**Array loop:**
```html
<bx:loop array="#products#" item="product">
    <div class="product">
        <h3><bx:output>#encodeForHTML( product.name )#</bx:output></h3>
        <p><bx:output>$#numberFormat( product.price, "0.00" )#</bx:output></p>
    </div>
</bx:loop>
```

**Struct loop:**
```html
<bx:loop struct="#config#" item="value" key="key">
    <bx:output><p>#key#: #value#</p></bx:output>
</bx:loop>
```

**Query loop:**
```html
<bx:loop query="#users#">
    <bx:output>
        <tr>
            <td>#users.id#</td>
            <td>#encodeForHTML( users.name )#</td>
            <td>#encodeForHTML( users.email )#</td>
        </tr>
    </bx:output>
</bx:loop>
```

**Index / count loop:**
```html
<bx:loop from="1" to="#items.len()#" index="i">
    <bx:output><li>#i#. #items[ i ].name#</li></bx:output>
</bx:loop>
```

### `<bx:include>` — Include Partial Templates

```html
<!-- Reuse header and footer partials -->
<bx:include template="/includes/header.bxm">

<main>
    <!-- page content -->
</main>

<bx:include template="/includes/footer.bxm">
```

Pass variables to the included template via the current scope (variables are
inherited by default in includes).

### `<bx:script>` — Inline Script Blocks

Switch to script mode within a template:

```html
<bx:script>
    var products  = productService.getFeatured( limit=6 )
    var totalPages = ceiling( products.recordCount / 10 )
</bx:script>

<bx:loop array="#products#" item="p">
    <bx:output><div>#p.name#</div></bx:output>
</bx:loop>
```

### `<bx:switch>` / `<bx:case>` — Switch Statements

```html
<bx:switch expression="#user.role#">
    <bx:case value="admin">
        <bx:include template="/views/admin-dashboard.bxm">
    </bx:case>
    <bx:case value="editor">
        <bx:include template="/views/editor-dashboard.bxm">
    </bx:case>
    <bx:defaultcase>
        <bx:include template="/views/user-dashboard.bxm">
    </bx:defaultcase>
</bx:switch>
```

### `<bx:try>` / `<bx:catch>` — Error Handling in Templates

```html
<bx:try>
    <bx:set var="data" value="#remoteService.getData()#">
    <bx:output><p>#data.message#</p></bx:output>
<bx:catch type="any" variable="e">
    <p class="error">Could not load data: <bx:output>#encodeForHTML( e.message )#</bx:output></p>
</bx:catch>
</bx:try>
```

---

## Mixing Script and Tag Syntax

You can freely mix script blocks and tag components in `.bxm` files:

```html
<bx:script>
    // Load data using script syntax
    var pageTitle    = "Product Catalog"
    var categories   = categoryService.getAll()
    var featuredIds  = url.keyExists( "cat" ) ? [ url.cat ] : []
</bx:script>

<!DOCTYPE html>
<html lang="en">
<head>
    <bx:output><title>#encodeForHTML( pageTitle )#</title></bx:output>
</head>
<body>
    <nav>
        <bx:loop array="#categories#" item="cat">
            <bx:output>
                <a href="/catalog?cat=#encodeForURL( cat.slug )#"
                   class="#featuredIds.contains( cat.id ) ? 'active' : ''#">
                    #encodeForHTML( cat.name )#
                </a>
            </bx:output>
        </bx:loop>
    </nav>
</body>
</html>
```

---

## Layout / View Patterns

### Nested Includes (Layout Pattern)

```html
<!-- views/layout.bxm -->
<!DOCTYPE html>
<html lang="en">
<head>
    <bx:output><title>#pageTitle ?: "My App"#</title></bx:output>
    <link rel="stylesheet" href="/css/app.css">
</head>
<body>
    <bx:include template="/views/partials/nav.bxm">
    <main>
        <bx:include template="#contentTemplate#">
    </main>
    <bx:include template="/views/partials/footer.bxm">
</body>
</html>
```

```html
<!-- In a page handler/template -->
<bx:set var="pageTitle" value="Dashboard">
<bx:set var="contentTemplate" value="/views/dashboard-content.bxm">
<bx:include template="/views/layout.bxm">
```

---

## HTML Output Encoding Reference

Always encode user-supplied data before rendering:

```html
<bx:output>
    <!-- HTML context — encode entities -->
    <p>#encodeForHTML( userBio )#</p>

    <!-- Attribute context -->
    <input value="#encodeForHTMLAttribute( formValue )#">

    <!-- URL context -->
    <a href="/profile?u=#encodeForURL( username )#">View Profile</a>

    <!-- JavaScript context -->
    <script>
        const name = "#encodeForJavaScript( displayName )#";
    </script>
</bx:output>
```

---

## Query of Queries in Templates

Use `<bx:query>` for database queries directly in templates (acceptable for
simple templates; prefer service layer for complex logic):

```html
<bx:script>
    var users = queryExecute(
        "SELECT id, name, email FROM users WHERE active = :active ORDER BY name",
        { active: { value: true, cfsqltype: "cf_sql_bit" } }
    )
</bx:script>

<table>
    <thead><tr><th>Name</th><th>Email</th></tr></thead>
    <tbody>
        <bx:loop query="#users#">
            <bx:output>
                <tr>
                    <td>#encodeForHTML( users.name )#</td>
                    <td>#encodeForHTML( users.email )#</td>
                </tr>
            </bx:output>
        </bx:loop>
    </tbody>
</table>
```

---

## Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| `#var#` outside `<bx:output>` | Wrap in `<bx:output>` |
| Raw user data in output | Use `encodeForHTML()` or `encodeFor="html"` |
| Logic-heavy templates | Move to service layer; keep templates presentational |
| Missing closing tags | `<bx:if>` requires `</bx:if>`, `<bx:loop>` requires `</bx:loop>` |
| Unscoped variables | Declare with `<bx:set>` or `<bx:script>` var statements |
| Including with absolute OS paths | Use web-root-relative paths: `/views/...` |