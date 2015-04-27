# Running An Application

Now that we have created our application, our first event handler controller and actions, let's run it via CommandBox.

```bash
server start
```

That commamnd will issue the startup of an embedded CFML engine on a port and open the browser for you and run our application.  You should now see your `hello world` being outputted to the whole world.

> **Info** The `server` namespace in CommandBox has some great commands to interact with the embedded server, so type `server help` to see all the help about the server namespace.