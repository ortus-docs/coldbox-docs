# My First Handler & View

## Handler Scaffolding

Now let's create our first controller, which in ColdBox is called **Event Handler**. Let's go to CommandBox again:

```bash
coldbox create handler name="hello" actions="index"
```

This will generate the following files:

* A new **handler** called `hello.cfc` inside of the `handlers` folder
* A **view** called `index.cfm` in the `views/hello` folder&#x20;
* An **integration test** at `tests/specs/integration/helloTest.cfc`.&#x20;

### Default URL Routing

Now go to your browser and enter the following URL to execute the generated event:

```
# With rewrites enabled
http://localhost:{port}/hello/index
```

You will now see a big `hello.index` outputted to the screen. You have now created your first handler and view combination.  However, how did this work?  It works as ColdBox by convention creates another agreement with you on how to execute events, default URL routing.

Your application router is located at : `config/Router.cfc`.  It will include a few default routes for you and the following default URL route:

```javascript
// Conventions based routing
route( ":handler/:action?" ).end();
```

This route tells ColdBox to look for the names of handlers (including directory names) and for names of the handler actions (functions).  The `?` on the `:action` portion denotes that the action might or might not exist in the URL.  If it doesn't exist, then another convention is in play, the default action which is `index`.

## Handler Code

Let's check out the handler code:

```javascript
component{

    /**
     * Default Action
     */
     function index( event, rc, prc ){
        event.setView( "hello/index" );
     }


}
```

As you can see, a handler is a simple CFC with functions on them. Each function maps to an **action** that is executed via the URL. The default action in ColdBox is `index()`which receives three arguments:

* `event` - An object that represents the request and can modify the response. We call this object the [request context](../../the-basics/request-context.md).
* `rc` - A struct that contains both `URL/FORM` variables (unsafe data)
* `prc` - A secondary struct that is **private** only settable from within your application (safe data)

{% hint style="info" %}
The **event** object is used for many things, in the case of this function we are calling a `setView()` method which tells the framework what view to render to the user once execution of the action terminates.
{% endhint %}

{% hint style="success" %}
**Tip:** The view is not rendered in line 7, but rendered after the execution of the action by the framework.
{% endhint %}

## Executing Events

Did you detect a convention here?

The sections in the URL are the same as the name of the event handler CFC (`hello.cfc`) and method that was generated `index()`. By convention, this is how you execute events in ColdBox by leveraging the following URL pattern that matches the name of a handler and action function.

{% hint style="success" %}
**Tip :** You can also nest handlers into folders and you can also pass the name of the folder(s) as well.
{% endhint %}

```
http://localhost:{port}/folder/handler/action
http://localhost:{port}/handler/action
http://localhost:{port}/handler
```

If no `action` is defined in the URL then the default action of `index` will be used.

All of this URL magic happens thanks to the URL mappings capabilities in ColdBox. By convention, you can write beautiful URLs that are RESTFul and by convention. You can also extend them and create more expressive URL Mappings by leveraging the `config/Router.cfc` which is your application router.

{% hint style="success" %}
**Tip:** Please see the [event handlers](../../the-basics/event-handlers/) guide for more in-depth information.
{% endhint %}

## My First Virtual Event

Now let's create a virtual event, which is basically just a view we want to execute with no event handler controller needed. This is a great way to incorporate _non-mvc_ files into ColdBox.  Migrating from a traditional application?

```bash
coldbox create view name="virtual/hello"
```

Open the view now (`/views/virtual/hello.cfm`) and add the following:

```markup
<h1>Hello from ColdBox Land!</h1>
```

Then go execute the virtual event:

```
http://localhost:{port}/virtual/hello
```

You will get the `Hello From ColdBox Land!` displayed! This is a great way to create tests or even bring in legacy/procedural templates into an MVC framework.

{% hint style="success" %}
**Tip:** You can see our [layouts and views](../../the-basics/layouts-and-views/) section for more in-depth information.
{% endhint %}
