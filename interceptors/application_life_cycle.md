# Application Life Cycle

|Interception Point|Intercept Structure|Description|
|--|--|--|
|afterConfigurationLoad |---|This occurs after the framework loads and your applications' configuration file is read and loaded. An important note here is that your application aspects have not been configured yet: bug reports, ioc plugin, validation, logging, and internationalization.|
|afterAspectsLoad |---|This occurs after the configuration loads and the aspects have been configured. This is a great way to intercept on application start.|
|preReinit |---|This occurs every time the framework is re-initialized|
|onException |exception - The *cfcatch* exception structure |This occurs whenever an exception occurs in the system. This event also produces data you can use to respond to it.|
|onRequestCapture |---|This occurs once the FORM/URL variables are merged into the request collection but before any event caching or processing is done in your application. This is a great event to use for incorporating variables into the request collections, altering event caching with custom variables, etc.|
|onInvalidEvent |<ul><li>invalidEvent - The invalid event</li><li>ehBean - The event handler bean object you can use to override the event</li><li>override - A flag that allows you to override the invalid event into an event of your choice</li></ul>|This occurs once an invalid event is detected in the application. This can be a missing handler or action. This is a great interceptor to use to alter behavior when events do not exist or to produce 404 pages.|
|applicationEnd |---|This occurs after a request is received and made ready for processing. This simulates an on request start interception point. Please note that this interception point occurs before the request start handler. |
|sessionStart |*session* structure |This occurs when a user's session starts|
|sessionEnd |<ul><li>sessionReference - A reference to the session structure</li><li>applicationReference - A reference to the application structure</li></ul>|This occurs when a user's session ends|
|preProcess |---|This occurs after a request is received and made ready for processing. This simulates an on request start interception point. Please note that this interception point occurs before the request start handler. |
|preEvent |<ul><li>processedEvent - The event that is about to be executed</li><li>eventArguments - A structure of arguments (if any) the event got executed with</li></ul>|This occurs before ANY event execution, whether it is the current event or called via the run event method. It is important to note that this interception point occurs before the preHandler convention. (See Event Handler Guide Chapter) |
|postEvent|<ul><li>processedEvent - The event that is about to be executed</li><li>eventArguments - A structure of arguments (if any) the event got executed with</li></ul>|This occurs after ANY event execution, whether it is the current event or called via the run event method. It is important to note that this interception point occurs after the postHandler convention (See Event Handler 
Chapter) |
||||
||||