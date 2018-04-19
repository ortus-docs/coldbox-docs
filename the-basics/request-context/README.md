# Request Context

On every request to a ColdBox event, the framework creates an object that models the incoming request. This object is called the Request Context object \(`coldbox.system.web.context.RequestContext`\) and it contains the incoming FORM/URL/REMOTE variables the client sent in and the object lives in the ColdFusion request scope. You will use this object in the controller and view layer of your application to get/set values, get metadata about the request, marshall data for RESTful request, and so much more.

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/RequestCollectionDataBus.jpg)

