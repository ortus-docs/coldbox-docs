# RESTful Action Routing

You can also use a structure map for the action argument so you can split the incoming HTTP method verbs into the appropriate actions:

```javascript
addRoute(
    pattern="/user/:username?", 
    handler="users", 
    action={
        GET = "list", POST = "create", PUT = "save", DELETE = "remove", HEAD ="info"
    }
);
```

Isn't that cool! Just like that you can split the incoming URL pattern to appropriate action executions according to the incoming HTTP verb.

## HTTP Method Spoofing

Although we have access to all these HTTP verbs, modern browsers still only support GET and POST. With ColdBox and HTTP Method Spoofing, you can take advantage of all the HTTP verbs in your web forms.

By convention, ColdBox will look for an `_method` field in the form scope. If one exists, the value of this field is used as the HTTP method instead of the method that was made. For instance, the following block of code would execute with the DELETE action instead of the POST action:

```markup
<cfoutput>
<form method="POST" action="#event.buildLink('posts/#prc.post.getId()#')#">
    <input type="hidden" name="_method" value="DELETE" />
    <button type="submit">Delete</button>
</form>
</cfoutput>
```

You can manually add these `_method` fields yourselves, or you can take advantage of ColdBox's HTML Helper.

```markup
<cfoutput>
#html.startForm( action = "posts.#prc.post.getId()#", method="DELETE" )#
    #html.submitButton( name = "Delete", class = "btn btn-danger" )#
#html.endForm()#
</cfoutput>
```

