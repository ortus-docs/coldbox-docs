---
description: Register a contacts datasource in the ColdFusion administrator and expose it as a ColdBox settings struct.
---

# Datasource

Make sure you register a datasource in your ColdFusion administrator, we called ours `contacts` and then register it in your ColdBox configuration so ColdBox can build datasource structs for us. This is totally optional, but we do it to showcase more injections:

`config/ColdBox.bx` (or `.cfc` for CFML)

```javascript
// Settings
settings = {
    contacts = {
        type = "mysql",
        name = "contacts"
    }
}
```
