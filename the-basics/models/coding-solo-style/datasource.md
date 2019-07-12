# Datasource

To use a datasource in ColdBox 5, add the Lucee datasource struct to your `config/Coldbox.cfc` configuration file settings:

```javascript
settings = {
    myDSN : {
        class: "com.microsoft.sqlserver.jdbc.SQLServerDriver",
        connectionString: "jdbc:sqlserver://HOST",
        connectionLimit: 5,
        username: "me",
        password: "plaintext_password"
    }
}
```

You can even use Coldbox's native `getSystemSetting()` function to pull database secrets from your Java system properties or environment variables:

```javascript
settings = {
    myDSN : {
        class : "com.microsoft.sqlserver.jdbc.SQLServerDriver",
        connectionString : "jdbc:sqlserver://#getSystemSetting("DB_CONNECTIONSTRING")#",
        username : getSystemSetting("DB_USER"),
        password : getSystemSetting("DB_PASSWORD")
    }
}
```
