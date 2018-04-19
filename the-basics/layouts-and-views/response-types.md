# Response Types

The event handlers will be the objects in question that will be responding to user requests and they have a few choices when it comes down to rendering content back:

* Set a view to be rendered back to the user \(with or without a layout\) 
* Render data in multiple view formats: JSON, XML, WDDX, HTML, TEXT or CUSTOM
* Do nothing or the infamous White Screen of Death
* Do an HTTP redirect to another event via `setNextEvent`

