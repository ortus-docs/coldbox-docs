# Running An Application

Now that we have created our application, our first event handler controller and actions, let's run it via CommandBox.

```bash
server start
```

The output of the command will be:

```
The server for /Users/lmajano/tmp/myapp is starting on 127.0.0.1:62604... type 'server status' to see result
```

That commamnd will issue the startup of an embedded CFML engine on a port and open the browser for you and run our application.  This will execute the default event in ColdBox which is `main.index` and present you with a welcome page much like the one shown below:



> **Info** The `server` namespace in CommandBox has some great commands to interact with the embedded server, so type `server help` to see all the help about the server namespace.