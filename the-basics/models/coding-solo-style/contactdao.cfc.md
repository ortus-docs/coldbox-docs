# ContactDAO.cfc

Our Contact DAO will talk to the datasource object we declared and do a few queries. Notice that this object is a singleton and has some dependency injection.

```text
coldbox create model name=ContactDAO persistence=singleton --open
```

Then spice it up

```javascript
component accessors="true" singleton{

    // Dependency Injection
    property name="dsn" inject="coldbox:datasource:contacts";

    function init(){
        return this;
    }

    query function getAll(){
        var q = new Query(datasource="#dsn.name#",sql="SELECT * FROM contacts");
        return q.execute().getResult();
    }

    query function getContact(required contactID){
        var q = new Query(datasource="#dsn.name#",sql="SELECT * FROM contacts where contactID = :contactID");
        q.addParam(name="contactID",value=arguments.userID,cfsqltype="cf_sql_numeric");
        return q.execute().getResult();
    }

    ... ALL OTHER METHODS HERE FOR CRUD ....

}
```

