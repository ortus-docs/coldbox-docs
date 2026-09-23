# ColdBox Docs Style Guide

Rules for writing and editing pages in this book.

## Page Shape

Every page follows this skeleton:

1. **Front-matter** — `description` (one sentence, for SEO/social) and `icon` (GitBook icon name). Both required.
2. **What/Why** — 2–3 sentences max. What it is, when you'd reach for it.
3. **First runnable example** — as early as possible. Show, then explain.
4. **Tasks / how-to sections** — the body, organized by what the reader wants to do.
5. **Reference tables** — options, settings, methods in tables, not prose.
6. **Edge cases & engine differences** — GitBook `{% hint %}` boxes.
7. **See Also** — 2–5 links to related pages.

## Length

- Concept pages: **400–1,200 words**. Over ~1,800 words → split into sub-pages.
- Reference pages (configuration, API-style) may exceed this.
- Pages under ~100 words with no unique content are merged into their parent as sections.
- What's-new/release pages are exempt.

## Language & Tone

- BoxLang first, always. BoxLang is the recommended language and runtime.
- Say **class**, not "CFC". When a filename example is `Something.cfc`, note it can also be a BoxLang class (`.bx`).
- The config file is the **"ColdBox class"** (`config/ColdBox.bx` or `config/ColdBox.cfc`). The bootstrapper is `Application.bx` or `Application.cfc` — never show only the `.cfc` variant.
- 🚀 **BoxLang Exclusive** badge for features only available on BoxLang.
- Emojis: allowed on landing, what's-new, and section-hub pages. Not in body/reference prose (badges excepted).

## Code Examples

- **Closures**: use the shorthand `() => {}` in BoxLang examples, never `function(){}` longhand. CFML tabs keep the long form for engine compatibility.
- **Classes**: every class-shaped example (handlers, models, services, interceptors, modules) uses GitBook tabs — BoxLang first, CFML second:

```markdown
{% tabs %}

{% tab title="BoxLang" %}

```javascript
class { ... }
```

{% endtab %}

{% tab title="CFML" %}

```javascript
component { ... }
```

{% endtab %}

{% endtabs %}
```

- Examples must be runnable or clearly excerpted — no pseudo-code presented as real code.
- Verify commands against the actual source (coldbox-cli, coldbox-platform) before documenting them. Never invent flags or subcommands.

## Cross-Linking Policy (No Double Documentation)

WireBox, CacheBox, and LogBox have their own books. ColdBox docs document **only how ColdBox consumes them**:

- ✅ Keep: the `cachebox`/`logbox`/`wirebox` config directives, the injection-DSL namespaces, event/view caching usage, interceptor events
- ❌ Don't: explain cache providers, appenders, binder mappings, AOP — link to [cachebox.ortusbooks.com](https://cachebox.ortusbooks.com), [logbox.ortusbooks.com](https://logbox.ortusbooks.com), [wirebox.ortusbooks.com](https://wirebox.ortusbooks.com)

Same rule for modules: an Ecosystem guide carries intro + install + one working example + link to the module's own book. Never reproduce a module's full docs.

## Ecosystem Guide Template

1. What it is (≤2 sentences)
2. `box install …`
3. Minimal working example (10–20 lines, BoxLang first, CFML tab)
4. When to use / alternatives
5. Prominent link to the module's book

## Diagrams

- Conceptual diagrams use **Mermaid** (` ```mermaid ` blocks), not raster images.
- Raster is only for UI screenshots and marketing art.
- Every flow that crosses 3+ components should have a diagram (request lifecycle, routing resolution, interceptor chain).

## Warnings & Behavior Changes

Use GitBook hints:

- `{% hint style="warning" %}` — behavior changes, breaking notes, deprecations
- `{% hint style="info" %}` — engine differences, clarifications
- `{% hint style="success" %}` — tips worth calling out

## What Not to Do

- No `function( arg )` longhand closures in BoxLang examples
- No bare `.cfc` references without the `.bx` alternative
- No duplicating WireBox/CacheBox/LogBox/module internals
- No marketing tone in reference pages
- No orphan pages: every file must appear in `SUMMARY.md` (redirect stubs excepted)
