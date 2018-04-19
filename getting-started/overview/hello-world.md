# Hello World

Let's create a simple hello world app now where we say **Hello World** back to the world! Incredibly exciting! Just go back to your CommandBox shell and type:

```bash
coldbox create controller name=hello actions=index,echo
```

Here is the output of the command:

```text
Created /Users/lmajano/tmp/myapp/views/hello/index.cfm
Created /Users/lmajano/tmp/myapp/views/hello/echo.cfm
Created /Users/lmajano/tmp/myapp/handlers/hello.cfc
Created /Users/lmajano/tmp/myapp/tests/specs/integration/helloTest.cfc
```

This will create the `hello.cfc` event handler controller in the `handlers` directory and create two action methods on it called `index() and echo()`. By default it also creates a folder called `hello` in the `views` directory and creates an `index.cfm and echo.cfm` inside of it. Finally, it will generate an integration test for your `hello` event handler inside of the `tests/integration/helloTest` folder. Wow! Pretty snazzy command!

**hello.cfc**

```javascript
/**
* I am a new handler
*/
component{

    function index(event,rc,prc){
        event.setView("hello/index");
    }    

    function echo(event,rc,prc){
        event.setView("hello/echo");
    }    

}
```

The two actions are already wired to display the appropriate views by calling the `setView()` method in the request context event object. However, let's change the `index()` action to just render out `Hello World` for us.

```javascript
function index(event,rc,prc){
    return "Hello World!";
}
```

That's it! Let's run this puppy now!

> **Info** The `coldbox create` command namespace has a plethora of creation and generation commands, check them out by typing `coldbox create help`

