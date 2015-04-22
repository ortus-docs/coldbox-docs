# Creating a 404 template via onInvalidEvent

### Introduction

In order to create a handler that will tackle 404 events you must use the onInvalidEvent setting from your configuration file. This setting traps whenever non-existent events will fire and executes this event instead. This event does not relocate but makes the event look as if it is still the one requested for. Why? In order to be able to implement 404 or correct 302 relocation algorithms.

### onInvalidEvent Setting
Open your configuration object and let's set this setting. If the setting does not exist already in the coldbox configuration structure, then just add it.

```js
coldbox = {
  onInvalidEvent = "main.pageNotFound"
}
```

I have mappend the invalid events to my main handler and *pageNotFound()* method action.

### Method
I can then go to my method and work on it accordingly. I will do two examples: 1) A simple render data page not found message, which we might not do for nice sites! 2) Set the 404 headers and render a nice view.

##### Method 1

```js
function pageNotFound(event,rc,prc){
  event.renderData(data="<h1>Page Not Found!</h1>",statusCode=404,statusMessage="Page Not Found!");
}
```

That's it! You thought there was more? Nope!

##### Method 2

This approach basically has an event that renders back a nice view to the user with maybe further instructions on how to proceed.

```js
function pageNotFound(event,rc,prc){
  event.renderData(data=renderView('main/pageNotFound'),statusCode=404,statusMessage="Page Not Found!");
}
```

Here is our sample view:

```js
<h1>Page not found</h1>

<div class="padded">
  <p>We're sorry, but we couldn't find the page you were looking for.</p>
  <p>Please check to make sure you entered the page address correctly and try again.</p>
</div>
```

That's it my friends. ColdBox, making development easy again!
