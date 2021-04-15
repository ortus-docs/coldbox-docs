# Models Namespace

The default namespace is not specifying one. This namespace is used to retreive either named mappings or full component paths.

### 1st Level DSL

| DSL | Description |
| :--- | :--- |
| `empty` | Same as saying _id_. Get a mapped instance with the same name as defined in the property, argument or setter method. |
| `id` | Get a mapped instance with the same name as defined in the property, argument or setter method. |
| `model` | Get a mapped instance with the same name as defined in the property, argument or setter method. |

### 2nd Level DSL

| `DSL` | Description |
| :--- | :--- |
| `model:{name}` | Get a mapped instance by using the second part of the DSL as the mapping name. |
| `id:{name}` | Get a mapped instance by using the second part of the DSL as the mapping name. |

### 3rd Level DSL

| `model:{name}:{method}` | Get the {name} instance object, call the {method} and inject the results |
| :--- | :--- |
| `id:{name}:{method}` | Get the {name} instance object, call the {method} and inject the results |

```javascript
// Let's assume we have mapped a few objects called: UserService, SecurityService and RoleService

// Empty inject, use the property name, argument name or setter name
property name="userService" inject;

// Using the name of the mapping as the value of the inject
property name="security" inject="SecurityService";

// Using the full namespace
property name="userService" inject="id:UserService";
property name="userService" inject="model:UserService";

// Simple factory method
property name="roles" inject="id:RoleService:getRoles";
```

