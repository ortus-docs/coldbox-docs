# The configure() Method

Once the public properties are set, we are now ready to configure our module. You will do this by creating a simple method called `configure()` and adding variables to the following configuration structures:


|Property|Type|Description|
|--|--|--|
|parentSettings|struct|Settings that will be appended and override the host application settings|
|settings|struct|Custom module settings that will only be available to the module. Please see the retrieving settings section|
|conventions|struct|A structure that explains the layout of the handlers, plugins, layouts and views of this module.|
|datasources|struct|A structure of datasource metadata that will append and override the host application datasources configuration|
|interceptorSettings|struct|A structure of settings for interceptor interactivity which includes the following sub-keys:<ul><li>customInterceptionPoints : A list of custom interception points to add to the application wide interceptor service</li></ul>|
|interceptors|array of structs|An array of declared interceptor structures that should be loaded in the entire application. Follows the same as the [ConfigurationCFC](http://wiki.coldbox.org/wiki/ConfigurationCFC.cfm) interceptor declarations.|
|layoutSettings |struct|A structure of elements that setup layout configuration data for the module with the following keys:<ul><li>defaultLayout : The name of the default layout to use for this module. Must be located in the layouts folder of the module.</li></ul>|
|routes|array|An array of declared URL routes or locations of routes for this module. The keys of the structure are the same as the *addRoute()* method of the [SES interceptor](http://wiki.coldbox.org/wiki/URLMappings.cfm) or a simple string location to the routes file to include.|
|wirebox|struct|A structure of [WireBox](http://wiki.coldbox.org/wiki/WireBox.cfm) configuration data, please refer to the [WireBox Configuration](http://wiki.coldbox.org/wiki/WireBox.cfm#Configure()_method) area or just use the injected binder object for mappings.|

It is important to note that there is only ONE running application and the modules basically leach on to it. So the following structures will be append their contents into the running application settings: parentSettings, datasources, webservices, customInterceptionPoints, and interceptors.

All of the configuration settings that are declared in your configuration object will be added to a key in the host application settings called modules. Inside of this structure all module configurations will be stored according to their module name and remember that the module name comes from the name of the directory on disk. So if you have a module called helloworld then the settings will be stored in the following location: ConfigSettings.modules.helloworld

Below is an example of some settings:

```js
function configure(){
    
  // parent settings
  parentSettings = {
    woot = "Module set it!"
  };

  // module settings - stored in the main configuration settings struct as modules.{moduleName}.settings
  settings = {
    display = "core"
  };

  // layout settings
  layoutSettings = {
    defaultLayout = "MyModuleLayout.cfm"
  };
  
  // Module Conventions
  conventions = {
    handlersLocation = "handlers",
    viewsLocation = "views",
    layoutsLocation = "layouts",
    pluginsLocation = "plugins",
    modelsLocation = "model"
  };
  
  // datasources
  datasources = {
    mysite   = {name="mySite", dbType="mysql", username="root", password="root"}
  };
  
  // web services
  webservices = {
    google = "http://news.google.com/news?pz=1&cf=all&ned=us&hl=en&topic=h&num=3&output=rss"
  };
  
  // SES Routes
  routes = [
    // load the routes.cfm in the config folder of the module
    "config/routes",
    // explicit route declared here
    {pattern="/api-docs", handler="api",action="index"}   
  ];    
  
  // Interceptor Config
  interceptorSettings = {
    customInterceptionPoints = "onModuleError"
  };
  interceptors = [
    {name="modulesecurity",class="#moduleMapping#.interceptors.ModuleSecurity",
     properties={
      URLMatch = '/api-docs',
      loginURL = '/api-docs/login'
     }
  ];  
  
  // binder mappings
  binder.map("MyModuleService").to("#moduleMapping#.model.ModuleService");
  // or easy map entire directory
  binder.mapDirectory("#moduleMapping#.model");
}
```

