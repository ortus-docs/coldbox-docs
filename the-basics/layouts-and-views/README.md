# Layouts & Views

ColdBox provides you with a very simple but flexible and powerful layout manager and content renderer. You no longer need to create module tags or convoluted broken up HTML anymore. You can concentrate on the big picture and create as many layouts as your application needs. Then you can programmatically change rendering schemas \(or skinning\) and also create composite views.

In this section we will explore the different rendering mechanisms that ColdBox offers and also how to utilize them. As you know, [event handlers](../event-handlers/) are our controller layer in ColdBox and we will explore how these objects can interact with the user in order to render content, whether HTML, JSON, XML or any type of rendering data.

## Conventions

Let's do a recap of our conventions for layouts and view locations:

```javascript
+ application
  + layouts
  + views
```

All your layouts will go in the `layouts` folder and all your views will go in the `views` folder.

## Response Types

The event handlers will be the objects in question that will be responding to user requests and they have a few choices when it comes down to rendering content back:

* Set a view to be rendered back to the user \(with or without a layout\) 
* Render data in multiple view formats: JSON, XML, WDDX, HTML, TEXT or CUSTOM
* Do nothing or the infamous White Screen of Death
* Do an HTTP redirect to another event via `setNextEvent`

