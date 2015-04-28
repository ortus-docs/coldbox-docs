# The configure() Method

Once the public properties are set, we are now ready to configure our module. You will do this by creating a simple method called *configure()* and adding variables to the following configuration structures:


|Property|Type|Description|
|--|--|--|
|parentSettings|struct|Settings that will be appended and override the host application settings|
|settings|struct|Custom module settings that will only be available to the module. Please see the retrieving settings section|
|conventions|struct|A structure that explains the layout of the handlers, plugins, layouts and views of this module.|
|datasources|struct|A structure of datasource metadata that will append and override the host application datasources configuration|
|webservices|struct|A structure of webservices metadata that will append and override the host application web services|
|interceptorSettings|struct|A structure of settings for interceptor interactivity which includes the following sub-keys:<ul><li>customInterceptionPoints : A list of custom interception points to add to the application wide interceptor service</li></ul>|
|interceptors|array of structs|An array of declared interceptor structures that should be loaded in the entire application. Follows the same as the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) interceptor declarations.|
|layoutSettings |struct|A structure of elements that setup layout configuration data for the module with the following keys:<ul><li>defaultLayout : The name of the default layout to use for this module. Must be located in the layouts folder of the module.</li></ul>|
||||
||||