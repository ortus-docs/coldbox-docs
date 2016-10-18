# Rendering Results

The `execute()` method has an argument called `renderResults` which defaults to **false**. If you pass in **true** then ColdBox will go through the normal rendering procedures and save the results in a request collection variable called: `cbox_rendered_content`. It will even work with `renderData()` or if you are returning RESTful information. Then you can easily assert what the content would have been for an event.

```js
event = execute(event="rest.api.users", renderResults=true);
```

You can then leverage the results HTML, JSON, XML or whatever, to even do assertions on them. Pretty Cool!
