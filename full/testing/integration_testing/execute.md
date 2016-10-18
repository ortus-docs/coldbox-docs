# The execute() method

The `execute()` method is your way of making requests in to your ColdBox application.  It can take the following parameters:

|Parameter Name|Parameter Type|Required|Default Value|Description|
|--|--|--|--|--|
| event | string | `false` | `""` | The event name to execute. e.g., `"Main.index"`. Either an event or a route must be provided. |
| route | string | `false` | `""` | The route to execute. e.g., `"/login"`.  The route parameter appends to your default SES Base Url set in `config/routes.cfm`.  Either an event or a route must be provided. |
| queryString | string | `false` | `""` | Any parameters to be passed as a query string.  These will be added to the Request Context for the test request. |
| private | boolean | `false` | `false` | If `true`, sets the event execution as private. |
| prePostExempt | boolean | `false` | `false` | If `true`, skips the pre- and post- interceptors. e.g., `preEvent`, `postHandler`, etc. |
| eventArguments | struct | `false` | `{}` | A collection of arguments to passthrough to the calling event handler method. |
