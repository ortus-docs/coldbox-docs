# Plugins

Plugins do not receive references to the collections or context object, they must request them via the following methods:

* getRequestContext() - Get access to the context reference
* getRequestCollection(boolean private=false) - Get access to the private or public request collection


