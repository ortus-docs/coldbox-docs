# ORMTransactionRollback Testing Decorator

ColdBox also comes with an MXUnit testing decorator to help you wrap all your test methods with transaction rollbacks if you are leveraging the Base[ ORM services](http://wiki.coldbox.org/wiki/ORM:BaseORMService.cfm) or just leveraging ORM on your own. This allows you to run your tests and then the decorator will rollback the transactions for you after the tests complete.

```js
/**
* @mxunit:decorators coldbox.system.testing.decorators.ORMTransactionRollback
*/
component extends="coldbox.system.testing.BaseTestCase"{

.. do your tests here all will rollback at the end.

}
```
