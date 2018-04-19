# EntityService Namespace

This injection namespace comes from the `cborm` module extensions \([https://github.com/ColdBox/cbox-cborm](https://github.com/ColdBox/cbox-cborm)\). It gives you the ability to easily inject base ORM services or binded virtual entity services for you:

| DSL | Description |
| --- | --- |
| entityService | Inject a BaseORMService object for usage as a generic service layer |
| entityService:{entity} | Inject a VirtualEntityService object for usage as a service layer based off the name of the entity passed in. |

```javascript
// Generic ORM service layer
property name="genericService" inject="entityService";
// Virtual service layer based on the User entity
property name="userService" inject="entityService:User";
```

