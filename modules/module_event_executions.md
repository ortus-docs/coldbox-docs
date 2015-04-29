# Module Event Executions

Module event executions are done almost exactly the same way we are used to in our ColdBox applications using event syntax patterns. By now we understand that an event comes in via the URL/FORM or Remotely to the framework and then the framework decides what event to execute. We also know that we can abstract our incoming events by using the ColdBox URL Routing, but at the end of the day, we will always have an `event` variable in the request collection that tells the framework what event to execute. The typical event syntax pattern we have learned is:

```js
// Pattern
event=[package.]handler[.action]

// Examples:
event=blog.posts
event=home.index
event=admin.dashboard.index
```

This typical approach maps the event to a package, handler and method combination. With the addition of modules our event syntax pattern now morphs to the following:

```js
// Pattern
event=[module:][package.]handler[.action]

// Examples:
event=blog:posts.index
event=cms:page.show
event=admin:dashboard
```

As you can see, we can prefix the event syntax with a module name and then followed by a colon `{module}:`. This is how ColdBox can know to what module to redirect the execution to. You can even execute the `runEvent()` methods and target modules:

```js
// Execute a viewlet in a module called viewlets:
#runEvent('viewlets:users.dashboard')#
```

> **Hint** Please remember that this is great for securing your applications as the event patterns you can match against with regular expressions will help you tremendously, as you can pinpoint modules directly.


In summary, the event syntax has been updated to support module executions via the `{module:}` prefix. However, please note that our preference is to abstract URLs and incoming event variables (via FORM/URL) by using ColdBox URL Routing. In the next section we will revise how to make module URL Routings work.

