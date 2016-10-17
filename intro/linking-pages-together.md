# Linking Pages Together

ColdBox provides you with a nice method for generating links between events by leveraging and object called `event` that is accessible in all of your layouts/views and event handlers.

Edit the `views/virtual/hello.cfm` page and wrap the content in a `cfoutput` and create a link to the main ColdBox event, which by convention is `main.index`.

```<cfoutput><h1>Hello from ColdBox Land!</h1><p><a href="#event.buildLink( "main.index" )"#>Go home</a></p></cfoutput>```

This code will generate a link to the `main.index` event in a search engine safe manner and in SSL detection mode.

> **Tip** You can visit our API Docs for further information about the `event` object and the `buildLink` method: http://apidocs.ortussolutions.com/coldbox/current/index.html?coldbox/system/web/context/RequestContext.html. For extra credit try to use more of the `buildLink` arguments.
