# Handler Returning Results

If you have written event handlers that actually return data, then you will have to get the values from the request collection. This is done in order to assert returned results from handlers when most likely this handler is called from a coldbox proxy or just returning HTML. The following are the keys created for you in the request collection.

|Key|Description|
|--|--|
|cbox_handler_results |The value returned from the event handler method. This key will NOT be created if the handler does not return any data.|