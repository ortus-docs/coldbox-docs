# ORM Events

|Interception Point|Intercept Structure|Description|
|--|--|--|
|ORMPostNew |{entity} |Called via the postNew() event|
|ORMPreLoad |{entity} |Called via the preLoad() event|
|Called via the preLoad() event|{entity} |Called via the postLoad() event|
|ORMPostDelete |{entity} |Called via the postDelete() event|
|ORMPreDelete |{entity} |Called via the preDelete() event|
|ORMPreUpdate |{entity,oldData} |Called via the preUpdate() event|
|ORMPostUpdate |{entity} |Called via the postUpdate() event|
|ORMPreInsert |{entity} |Called via the preInsert() event|
|ORMPostInsert |{entity} |Called via the postInsert() event|
