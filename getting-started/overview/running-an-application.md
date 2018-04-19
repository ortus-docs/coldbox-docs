# Running An Application

Now that we have created our application, our first event handler controller and actions, let's run it via CommandBox.

```bash
server start
```

The output of the command will be:

```text
The server for /Users/lmajano/tmp/myapp is starting on 127.0.0.1:62604... type 'server status' to see result
```

This command will issue the startup of an embedded CFML engine on a port and open the browser for you running our application. This will execute the default event in ColdBox which is `main.index` and present you with a welcome page much like the one shown below:

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/app_template.png)

> **Info** The `server` namespace in CommandBox has some great commands to interact with the embedded server, so type `server help` to see all the help about the server namespace. You can change ports, SSL, rewrite rules, and a lot more.

You can now execute your hello world action by either clicking on the `hello` link in the **Registered Event Handlers** section or navigating to the following URL to execute the action: `http://localhost:{port}/index.cfm/hello`. You will notice there is no `index` in the URL, this is because `index` is the default action for events. However, you can add it if you like: `http://localhost:{port}/index.cfm/hello/index`.

> **Info** ColdBox will parse the incoming URL route to form an executable event string. The default convention for the URL routing is `/:module/:package/:handler/:action`

