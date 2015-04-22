# Event Handlers

All event handlers receive 3 arguments whenever you declare actions in them:

* event - The request context object
* rc - A reference to the request collection structure
* prc - A reference to the private request collection structure

`function list(event,rc,prc){}`

Receiving the references makes a huge performance difference as you can interact with the structures instead of methods in the event object. So as a tip, try to interact with the structures as much as you can.


