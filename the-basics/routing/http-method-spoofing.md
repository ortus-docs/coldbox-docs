# HTTP Method Spoofing

Although we have access to all the HTTP verbs, modern browsers still only support **GET** and **POST**. With ColdBox and HTTP Method Spoofing, you can take advantage of **all** the HTTP verbs in your web forms.

By convention, ColdBox will look for an `_method` field in the **FORM** scope. If one exists, the value of this field is used as the HTTP method instead of the method from the execution. For instance, the following block of code would execute with the **DELETE** action instead of the **POST** action:

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

