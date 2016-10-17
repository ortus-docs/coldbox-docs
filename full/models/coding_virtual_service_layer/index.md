# Coding: Virtual Service Layer

Now let's build the same thing but using ColdFusion ORM and our [Virtual Service Layer](http://wiki.coldbox.org/wiki/ORM:VirtualEntityService.cfm) approach, in which we will use a service layer but virtually built by ColdBox. This will most likely give you 80% of what you would ever need, but in case you need to create your own and customize, then you would build a service object that extends or virtual or base layer.


```js
+ handlers 
  + contacts.cfc
+ models
  + Contact.cfc
```
