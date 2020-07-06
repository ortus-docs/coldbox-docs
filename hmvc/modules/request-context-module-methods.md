# Request Context Module Methods

The request context object \(`event` parameter received in handlers/layouts/views\) has also been expanded to have the following module methods:

* `getCurrentModule()` : Returns the module name of the currently executing module event.
* `getModuleRoot([moduleName])` : Returns the web accessible root to the module's root directory. If you do not pass the explicit module name, we will default to use the `getCurrentModule()` in the request.

The last method is what is really interesting when building visual modules that need assets from within the module itself. You can easily target the web-accessible path to the module by using the `getModuleRoot()` method. Below are some examples:

```javascript
<head>
  <meta name="author" content="Luis Majano" />
  <meta http-equiv="content-type" content="text/html;charset=utf-8" />

    <---< Base HREF if using SES --->
  <cfif event.isSES()>
    <base href="#getSetting('htmlBaseURL')#">
  </cfif>

    <---<Module  CSS --->
  <link rel="stylesheet" href="#event.getModuleRoot()#/includes/css/style.css" type="text/css" />
  <link rel="stylesheet" href="#event.getModuleRoot()#/includes/js/ratings/jquery.ratings.css" type="text/css" />

  <---< Module Javascript --->
  <script type="text/javascript" src="#event.getModuleRoot()#/includes/js/jquery-latest.pack.js"></script>
  <script type="text/javascript" src="#event.getModuleRoot()#/includes/js/forgebox.js"></script>
  <script type="text/javascript" src="#event.getModuleRoot()#/includes/js/jquery.simplemodal-latest.min.js"></script>
  <script type="text/javascript" src="#event.getModuleRoot()#/includes/js/jquery.uidivfilter.js"></script>
  <script type="text/javascript" src="#event.getModuleRoot()#/includes/js/ratings/jquery.ratings.pack.js"></script>

  <title>The Awesome ForgeBox Module!</title>
</head>
```

As you can see, this is essential when building module UIs or layouts.

