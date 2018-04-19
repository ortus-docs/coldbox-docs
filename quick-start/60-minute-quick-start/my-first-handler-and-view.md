# My First Handler & View

Now let's create our first controller, which in ColdBox is called **Event Handler**. Let's go to CommandBox again:

```bash
coldbox create handler name="hello" actions="index"
```

This will generate a new handler called `hello.cfc` inside of the `handlers` folder, a view called `index.cfm` in the `views/hello` folder and even an integration test at `tests/specs/integration/helloTest.cfc`. Now go to the following URL to execute the generated event:

```text
# With rewrites enabled
http://localhost:{port}/hello/index

# With no rewrites enabled
http://localhost:{port}/index.cfm/hello/index
```

You will now see a big `hello.index` outputted to the screen. You have now created your first handler and view combination.

## Executing Events

Did you detect a convention here? 

The sections in the URL are the same as the name of the event handler CFC \(`hello.cfc`\) and method that was generated \(`index()`\). By convention, this is how you execute events in ColdBox by leveraging the following URL pattern that matches the name of a handler and action method. 

{% hint style="info" %}
 You can also nest handlers into folders and you can also pass the name of the folder\(s\) as well.
{% endhint %}

```text
http://localhost:{port}/folder/handler/action
http://localhost:{port}/handler/action
http://localhost:{port}/handler
```

If no `action` is defined then the default action of `index` will be used. This is leveraging the RESTFul URL mappings in ColdBox. You can configure them by leveraging the `config/Router.cfc` which is your application router.

{% hint style="success" %}
**Tip:** Please see the [event handlers](../../the-basics/event-handlers/) guide for more in-depth information.
{% endhint %}

## My First Virtual Event

Now let's create a virtual event, which is basically just a view we want to execute with no event handler controller needed.  This is a great way to incorporate non-mvc files into ColdBox, baby steps!

```bash
coldbox create view name="virtual/hello"
```

Open the view now \(`/views/virtual/hello.cfm`\) and add the following:

```markup
<h1>Hello from ColdBox Land!</h1>
```

Then go execute the virtual event:

```text
http://localhost:{port}/virtual/hello
```

You will get the `Hello From ColdBox Land!` displayed! This is a great way to create dynamic views or even bring in legacy/procedural templates into an MVC framework.

> **Tip** You can find much more information about layouts and views in our [full docs](../../the-basics/layouts-and-views/)

