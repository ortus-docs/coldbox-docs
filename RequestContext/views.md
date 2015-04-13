# Views

All layouts and views have the following variables declared for you in the variables scope:

* event - The request context object
* rc - A reference to the request collection structure
* prc - A reference to the private request collection structure

So there is no need to know where stuff comes from, it is there already, just use it. Again, try to interact with the structures as much as you can so you can increase performance.