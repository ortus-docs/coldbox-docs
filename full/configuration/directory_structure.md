# Directory Structure

Now that we have seen the [core conventions](conventions.md), we can layout our application.  Most of the time we would recommend you use the shipped application templates in the ColdBox bundle download or by leveraging the CommandBox ColdBox creation commands.

To get started with a structure go into CommandBox interactive shell and let's create a ColdBox application:

```bash
CommandBox>coldbox create app name=MyApp skeleton=AdvancedScript
```

This will create a Coldbox application with the following structure:

<img src="https://coldbox.ortusbooks.com/content/images/ApplicationTemplate.png">

> **Info**: Please note that the only required files/directories are `Application.cfc, index.cfm and handlers`.  The rest are completely optional.

| File/Directory | Description |
| -- | -- |
| Application.cfc | Every ColdFusion application needs one. This includes the basic ColdBox bootstrap code inside of it.
| box.json | The CommandBox package descriptor file
| favicon.ico | A nice site favicon
| includes | Where all JavaScript, CSS and static assets go
| index.cfm | The front controller that dispatches ColdBox requests
| interceptors | An optional folder where you can store ColdBox Interceptors
| lib | Location where you can drop in an Java class or jar to be available to the CFML engine
| remote | An optional folder where you can store remote proxies, RESTFul or SOAP web services
| tests | Yes, we have to do it. The location of your TestBox test harness

## Application Templates

The official ColdBox application templates can be found in our github page: https://github.com/coldbox-templates.  Please note that CommandBox's `skeleton` argument can use any of the aliases below or any CommandBox endpoint. This means you can have your own templates.

| Template | Description |
| -- | -- |
| Advanced | The advanced template that includes every convention and optional configuration in tag format
| AdvancedScript | The advanced template that includes every convention and optional configuration in script format
| elixir | Advanced script template with ColdBox elixir support for asset pipelines
| elixir-bower | Advanced script template with ColdBox elixir support for asset pipelines and bower support
| elixir-vuewjs | Advanced script template with ColdBox elixir support for asset pipelines and VueJS integration
| rest | A RESTFul services application
| Simple | A fairly simple structure
| SuperSimple | The basics
