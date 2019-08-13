# ContactDAO.cfc

Our Contact DAO will talk to the datasource object we declared and do a few queries. Notice that this object is a singleton and has some dependency injection.

```text
coldbox create model name=ContactDAO persistence=singleton --open
```

Then spice it up

```javascript
component accessors="true" singleton{

    // Dependency Injection
    property name="dsn" inject="coldbox:setting:contacts"

    function init(){
        return this;
    }

    query function getAll(){
        var sql = "SELECT * FROM contacts";
        return queryExecute( sql, {}, { datasource: dsn.name } );
    }

    query function getContact(required contactID){
        var params = {
            contactID: { value: arguments.contactID, cfsqltype: "numeric" }
        };
        var sql = "SELECT * FROM contacts where contactID = :contactID";
        return queryExecute( sql, params, { datasource: dsn.name } );
    }

    ... ALL OTHER METHODS HERE FOR CRUD ....

}
```

