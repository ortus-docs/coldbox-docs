# Dynamic handler\/action Placeholders

You can also use the reserved position placeholders `:handler` and `:action` so you can specifically position them in the URL dyamically, meaning their names comes from the URL:

```javascript
// route with regex constraint
addRoute( pattern="/admin/users/:action", handler="admin.users" );
addRoute( pattern="/admin/:handler/:action?");
```

