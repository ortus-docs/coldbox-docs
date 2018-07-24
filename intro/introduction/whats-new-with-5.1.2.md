# What's New With 5.1.2

This is a patch release for ColdBox

## Bugs/Regressions

* \[[COLDBOX-711](https://ortussolutions.atlassian.net/browse/COLDBOX-711)\] - HTML helper cannot add fontawesome icons in button due to XSS enabled by default
* \[[COLDBOX-712](https://ortussolutions.atlassian.net/browse/COLDBOX-712)\] - ColdBox shutdown code that uses CF mappings for modules fails on fwreinit
* \[[COLDBOX-713](https://ortussolutions.atlassian.net/browse/COLDBOX-713)\] - addAsset throw error when called from handlers

## Improvements

* \[[COLDBOX-714](https://ortussolutions.atlassian.net/browse/COLDBOX-714)\] - Too many issues when encoding by default for HTML Helper, revert to non-encoded and provide ways to encode globally and a-la-carte

## HTML Helper Changes

The HTML Helper has been migrated to an internal module in this release.  It allows you to configure it via the following configuration settings in your `ColdBox.cfc`.

```javascript
moduleSettings = {
    htmlHelper = {
        // Base path for loading JavaScript files
        js_path = "",
        // Base path for loading CSS files
        css_path = "",
        // Auto-XSS encode values when using the HTML Helper output methods
        encodeValues = false
    }
}
```

### Injection Shortcut

You can also now inject the HTML helper anywhere using it's injection DSL Shortcut of `@HTMLHelper`

