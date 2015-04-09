# Datasource

Make sure you register a datasource in your ColdFusion administrator, we called ours contacts and then register it in your ColdBox configuration so ColdBox can build datasource objects for us. This is totally optional, but we do it to showcase more injections and dealing with more objects.

```js
datasources = {
	contacts = {name="contacts", dbtype="mySQL"}
};
```

