# Building Routable Links

In your views, layouts and handlers you can use the `buildLink` method provided by the request context object to build routable links in your applicaiton.

```js
buildLink(
    any linkTo, 
    [boolean translate='true'], 
    [boolean ssl], 
    [any baseURL=''], 
    [any queryString='']
) 
```

ust pass in the routed URL or event and it will create the appropriate URL for you:

```js
<a href="#event.buildLink( 'home.about' )#">About</a>
<a href="#event.buildLink( 'user.edit.id.#user.getID()#' )#">Edit User</a>
```

> **Info** The buildlink will translate any periods (.) to (/) slashes for you automatically


