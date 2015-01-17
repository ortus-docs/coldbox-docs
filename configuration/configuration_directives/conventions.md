# Conventions

This element defines custom conventions for your application. By default, the framework has a default set of conventions that you need to adhere too. However, if you would like to implement your own conventions for a specific application, you can use this setting, otherwise do not declare it:

```js
//Conventions
conventions = {
	handlersLocation = "controllers",
	pluginsLocation  = "plugins",
	viewsLocation 	 = "views",
	layoutsLocation  = "views",
	modelsLocation 	 = "model",
	modulesLocation  = "modules",
	eventAction 	 = "index"
};
```