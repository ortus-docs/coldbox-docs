# Datasource

Make sure you register a datasource in your ColdFusion administrator, we called ours `contacts` and then register it in your ColdBox configuration so ColdBox can build datasource structs for us. This is totally optional, but we do it to showcase more injections:

`config/ColdBox.cfc`

```javascript
// Settings
settings = {
    contacts = {
        type = "mysql",
        name = "contacts"
    }
}
```

