# Hello World

Let's create a simple hello world app now where we say **Hello World** back to the world! Incredibly exciting! Just go back to your CommandBox shell and type:

```bash
coldbox create controller name=hello actions=index,echo
```

Here is the output of the command:
```
Created /Users/lmajano/tmp/myapp/views/hello/index.cfm
Created /Users/lmajano/tmp/myapp/views/hello/echo.cfm
Created /Users/lmajano/tmp/myapp/handlers/hello.cfc
Created /Users/lmajano/tmp/myapp/tests/specs/integration/helloTest.cfc
```

This will create the `hello.cfc` event handler controller in the `handlers` directory and create two action methods on it called `index() and echo`.  It will by default also create a folder called `hello` in the `views` directory and create a `index.cfm and echo.cfm` inside of it.  Finally, it will also generate an integration test for your `hello` event handler inside of the `tests/integration/helloTest` folder.  Wow! Pretty snazzy command!

