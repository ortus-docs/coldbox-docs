# ORMTransactionRollback Testing Decorator

ColdBox also comes with an MXUnit testing decorator to help you wrap all your test methods with transaction rollbacks if you are leveraging the Base ORM services or just leveraging ORM on your own. This allows you to run your tests and then the decorator will rollback the transactions for you after the tests complete.

