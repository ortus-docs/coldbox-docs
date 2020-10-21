# HTTP Method Security

More often you will find that certain web operations need to be restricted in terms of what HTTP verb is used to access a resource. For example, you do not want form submissions to be done via **GET** but via **POST** or **PUT** operations. HTTP Verb recognition is also essential when building strong RESTFul APIs when security is needed as well.

## Manual Solution

You can do this manually, but why do the extra coding :\)

```javascript
function delete(event,rc,prc){
    // determine incoming http method
    if( event.getHTTPMethod() == "GET" ){
        flash.put("notice","invalid action");
        relocate("users.list");
    }
    else{
        // do delete here.
    }
}
```

This solution is great and works, but it is not THAT great. We can do better.

## Allowed Methods Property

Another feature property on an event handler is called `this.allowedMethods`. It is a declarative structure that you can use to determine what the allowed HTTP methods are for any action on the event handler.

```javascript
this.allowedMethods = {
    actionName : "List of approved HTTP Verbs"
};
```

If the request action HTTP method is not found in the approved list, it will look for a `onInvalidHTTPMethod()` on the handler and call it if found. Otherwise ColdBox throws a **405 exception** that is uniform across requests.

{% hint style="info" %}
You can listen for [global invalid HTTP](../../getting-started/configuration/coldbox.cfc/configuration-directives/) methods using the `coldbox.onInvalidHTTPMethodHandler` located in your `config/ColdBox.cfc.`
{% endhint %}

```javascript
component{

    this.allowedMethods = {
        delete : "POST,DELETE",
        list   : "GET"
    };

    function list(event,rc,prc){
        // list only
    }

    function delete(event,rc,prc){
        // do delete here.
    }
}
```

{% hint style="info" %}
If the **action** is not listed in the structure, then it means that we allow **all** HTTP methods. Just remember to either use the `onError()` or `onInvalidHTTPMethod()` method conventions or an exception handler to deal with the security exceptions.
{% endhint %}

## Allowed Methods Annotation

You can tag your event actions with a `allowedMethods` annotation and add a list of the allowed HTTP verbs as well. This gives you a nice directed ability right at the function level instead of a property. It is also useful when leveraging DocBox documentation as it will show up in the API Docs that are generated.

```javascript
function index( event, rc, prc) allowedMethods="GET,POST"{
    // my code here
}
```

