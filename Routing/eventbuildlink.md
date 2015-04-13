# event.buildLink()

```js
public any buildLink(string linkto, [boolean translate='true'], [boolean ssl='false'], [string baseURL=''], [string queryString=''])
```

The request context has a method called buildLink() that will build SES URLs for you. Just pass in the route or event and it will create the appropriate URL for you:

```js
<a href="#event.buildLink('home.about')#">About</a>
<a href="#event.buildLink('user.edit.id.#user.getID()#')#">Edit User</a>
```

> **Info** The buildlink will translate any periods (.) to (/) slashes for you automatically


