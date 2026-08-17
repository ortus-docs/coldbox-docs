# HTTP Method Spoofing

Although we have access to all the HTTP verbs, modern browsers still only support **GET** and **POST**. With ColdBox and HTTP Method Spoofing, you can take advantage of **all** the HTTP verbs in your web forms.

By convention, ColdBox will look for an `_method` field in the **FORM** scope. If one exists, and the transport-level request is a **POST**, its value is used as the HTTP method instead of `POST` — for `PUT`, `PATCH`, or `DELETE` targets only. For instance, the following block of code would execute with the **DELETE** action instead of the **POST** action:

```markup
<cfoutput>
<form method="POST" action="#event.buildLink( 'posts/#prc.post.getId()#' )#">
    <input type="hidden" name="_method" value="DELETE" />
    <button type="submit">Delete</button>
</form>
</cfoutput>
```

You can manually add these `_method` fields yourselves, or you can take advantage of ColdBox's HTML Helper `startForm()` method.  Just pass the method you like, we will take care of the rest:

```markup
<cfoutput>
#html.startForm( action = "posts.#prc.post.getId()#", method="DELETE" )#
    #html.submitButton( name = "Delete", class = "btn btn-danger" )#
#html.endForm()#
</cfoutput>
```

{% hint style="danger" %}
**Security:** the `_method` override is only honored when the *original* transport-level request method is `POST`. A `GET` request carrying `?_method=DELETE` is returned as `GET` unchanged — this prevents a plain link, an `<img>` tag, a crawler, or a browser prefetch from silently triggering a destructive HTTP verb. Only `PUT`, `PATCH`, and `DELETE` are honored as override targets; `?_method=GET` on a `POST` request is likewise ignored and the original `POST` is kept.
{% endhint %}

| Original method | `_method` | `event.getHTTPMethod()` result |
| ---------------- | ---------- | -------------------------------- |
| `GET`             | `DELETE`   | `GET` — override ignored           |
| `HEAD`            | `DELETE`   | `HEAD` — override ignored          |
| `POST`            | `DELETE`   | `DELETE` ✓                          |
| `POST`            | `PUT`      | `PUT` ✓                             |
| `POST`            | `PATCH`    | `PATCH` ✓                           |
| `POST`            | `GET`      | `POST` — invalid override ignored  |

## Getting the Original Method

`event.getHTTPMethod()` returns the *effective* method after spoofing is applied - the one your routes and `withVerbs()`/`allowedMethods` checks match against. If you need the raw, un-spoofed transport-level method instead (for logging, auditing, or a security interceptor), use `event.getOriginalHTTPMethod()`:

```javascript
event.getHTTPMethod();          // "DELETE" - after _method spoofing is applied
event.getOriginalHTTPMethod();  // "POST" - the real transport-level verb, always
```
