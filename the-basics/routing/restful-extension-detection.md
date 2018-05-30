# RESTFul Extension Detection

ColdBox allows you to detect incoming extensions from incoming paths automatically for you.  This is great for building multi-type responses or to just create virtual extensions for events.

```text
http://localhost/users
http://localhost/users.json
http://localhost/users.xml
http://localhost/users.pdf
http://localhost/users.html    
```

If an extension is detected in the incoming URL, ColdBox will grab it and store it in the request collection \(`RC`\) as the variable `format`.

```text
http://localhost/users => rc.format is null, does not exist
http://localhost/users.json => rc.format = json
http://localhost/users.xml => rc.format = xml
http://localhost/users.pdf => rc.format = pdf
http://localhost/users.html => rc.format = html
```

