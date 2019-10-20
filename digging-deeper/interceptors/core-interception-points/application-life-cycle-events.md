# Application Life Cycle Events

| Interception Point | Intercept Structure | Description |
| :--- | :--- | :--- |
| afterConfigurationLoad | --- | This occurs after the framework loads and your applications' configuration file is read and loaded. An important note here is that your application aspects have not been configured yet and no modules have been loaded. |
| afterAspectsLoad | --- | This occurs after the configuration loads and the aspects have been configured. This is a great way to intercept on application start after all modules have loaded. |
| preReinit | --- | This occurs every time the framework is re-initialized |
| onException | exception - The _cfcatch_ exception structure | This occurs whenever an exception occurs in the system. This event also produces data you can use to respond to it. |
| onRequestCapture | --- | This occurs once the FORM/URL variables are merged into the request collection but before any event caching or processing is done in your application. This is a great event to use for incorporating variables into the request collections, altering event caching with custom variables, etc. |
| onInvalidEvent | invalidEvent - The invalid event | This occurs once an invalid event is detected in the application. This can be a missing handler or action. This is a great interceptor to use to alter behavior when events do not exist or to produce 404 pages. |
| applicationEnd | --- | This occurs after a request is received and made ready for processing. This simulates an on request start interception point. Please note that this interception point occurs before the request start handler. |
| sessionStart | _session_ structure | This occurs when a user's session starts |
| sessionEnd | sessionReference - A reference to the session structure | This occurs when a user's session ends |
| preProcess | --- | This occurs after a request is received and made ready for processing. This simulates an on request start interception point. Please note that this interception point occurs before the request start handler. |
| preEvent | processedEvent - The event that is about to be executed | This occurs before ANY event execution, whether it is the current event or called via the run event method. It is important to note that this interception point occurs before the preHandler convention. \(See Event Handler Guide Chapter\) |
| postEvent | processedEvent - The event that is about to be executed | This occurs after ANY event execution, whether it is the current event or called via the run event method. It is important to note that this interception point occurs after the postHandler convention \(See Event Handler Chapter\) |
| postProcess | --- | This occurs after rendering, usually the last step in an execution. This simulates an on request end interception point. |
| preProxyResults | {proxyResults} | This occurs right before any ColdBox proxy calls are returned. This is the last chance to modify results before they are returned. |

