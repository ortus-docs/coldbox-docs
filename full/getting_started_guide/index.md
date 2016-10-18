# ColdBox Getting Started Guide

The ColdBox MVC Platform is the de-facto enterprise-level MVC framework for CFML developers.  It's professionally backed, highly extensible, and productive.  Getting started with ColdBox is quick and painless.  The only thing you need to begin is [CommandBox](http://www.ortussolutions.com/products/commandbox), a command line tool for CFML developers.   

## Install CommandBox 

You can read through our one-page [CommandBox Getting Started Guide](http://ortus.gitbooks.io/commandbox-documentation/content/getting_started_guide.html).   Or simply grab the CommandBox executable from the [download page](http://www.ortussolutions.com/products/commandbox#download) and double click it to run.  

http://www.ortussolutions.com/products/commandbox

You should now be seeing a prompt that looks like this:

![CommandBox Terminal](/images/commandbox-terminal.png)

## Create A New Site

Now we're cooking with gas!  Let's create a new ColdBox site.  CommandBox comes with built-in commands for scaffolding out new sites as well as installing ColdBox and other libraries.  We'll start by changing into an empty directory were we want our new site to live.  If necessary, you can create a new folder.

```bash
CommandBox> mkdir C:\playground
CommandBox> cd C:\playground
```

Now let's ask CommandBox to create a new ColdBox site for us.  The `--installColdBox` flag will also install the latest version of the ColdBox Platform alongside our new app skeleton.

```bash
CommandBox> coldbox create app myApp --installColdBox
```

This command will place several new folders and files in your working directory.  Let's run the `ls` command to view them.

```bash
CommandBox> ls
```

Here's a rundown of the important bits.
* **coldbox/** - This is the ColdBox framework
* **models/** - This holds your app's CFCs 
* **views/** - Your HTML views will go here
* **handlers/** - This holds the app's controllers
* **config/** - Modify your apps settings here

## Start It Up

Now that our shiny new MVC app is ready to go, let's fire it up using the embedded server built into CommandBox.  You don't need any other software installed on your PC for this to work.  CommandBox has it all!

```bash
CommandBox> start --rewritesEnable
```

In a few seconds, a browser window will appear with your running application. This is a full server with access to the web administrator where you can add data sources, mappings, or adjust the server settings. Notice the handy icon added to your system tray as well.  The `--rewritesEnable` flag will turn on some basic URL rewriting so we have nice, pretty URLs. 

![Default ColdBox App Template](/images/app_template.png)

## Take A Look Around

ColdBox uses easy conventions to define the controllers and views in your app.  Let's open up our main app controller in your default editor to have a looksie.

```bash
CommandBox> edit handlers/main.cfc
```

At the top, you'll see a method named "index".  This represents the default action that runs for this controller.  

```javascript
// Default Action
function index(event,rc,prc){
	prc.welcomeMessage = "Welcome to ColdBox!";
	event.setView("main/index");
}
```

Now let's take a look in the "main/index" view.  It's located int he `views` folder.

```bash
CommandBox> edit views/main/index.cfm
```

This line of code near the top of the view is what outputs the `prc.welcomeMessage` variable we set in the controller.

```html
<h1>#prc.welcomeMessage#</h1>
```

Try changing the value being set in the handler and refresh your browser to see the change. 

```javascript
prc.welcomeMessage = "This is my new welcome message";

```

## Building On

Let's define a new controller now.  Sometimes you'll see these called "handlers".  This is because ColdBox is an event-driven system.  Your controllers act as event handlers to respond to requests, REST API, or remote proxies.   

Pull up CommandBox again and run this command.

```bash
CommandBox> coldbox create handler helloWorld index,add,edit,list
```

That's it!  You don't need to add any specicial configuration to declare your handler.  Now we have a new handler called `helloWorld` with actions `index`, `add`, `edit`, and `list`.   The command also created a test case for our handler as well as stubbed-out views for each of the actions.  

Now, let's re-initalize the framework to pick up our new handler by typing `?fwreinit=1` at the end of the URL.  

Let's hit this new controller we created with a URL like so.  Your port number will probalby be different.

> 127.0.0.1:43272/helloWorld

Normally the url would have `index.cfm` before the `/helloWorld` bit, but our `--rewritesEnable` flag when we started the server makes this nicer URL possible.

## Install Packages

ColdBox's MVC is simple, but it's true power comes from the wide selection of modules you can install into your app to get additional functionality.  You can checkout the full list of modules available on the Forgebox site.
> [forgebox.io/type/modules](http://forgebox.io/type/modules)

Here's some useful examples:
* **cbdebugger** -- For debugging Coldbox apps
* **cbmessagebox** -- Display nice error/success messages
* **cborm** -- Awesome ORM Services
* **cb18n** -- For multilingual sites
* **BCrypt** -- Industry-standard password hashing
 
Install `cbmessagebox` from the CommandBox prompt like this:

```bash
CommandBox> install cbmessagebox
```

We can see the full list of packages by using the `list` command. 

```bash
CommandBox> list
Dependency Hierarchy for myApp (0.0.0)
+-- cbmessagebox (1.0.0)
+-- coldbox (4.0.0)
```

Right now we can see that our app depends on `coldbox` and `cbmessagebox` to run.  We'll use our new `cbmessagebox` module in a few minutes.  But first, we'll create a simple Model CFC to round out our `MVC` app.

## Creating A Model

Models encapsulate the business logic your application.  They can be services, beans, or DAOs.  We'll use CommandBox to create a `GreeterService` in our new app with a `sayHello` method.

```bash
CommandBox> coldbox create model GreeterService sayHello --open
```

The `--open` is a nice shortcut that opens our new model in our default editor after creating it.  Let's finish implementing the `sayHello()` method by adding this return statement and save the file.

We can also add the word `singleton` to the component declaration.  This will tell WireBox to only create one instance of our service.

```javascript
component singleton {

    function sayHello(){
    	return 'Hey, you sexy thing!';
    }
    
}
```

## Tie It All Together

Ok, let's open up that `helloWorld` handler we created a while back.  Remember, you can hit tab while typing to auto-complete your file names.

```bash
CommandBox> edit handlers/helloWorld.cfc
```

We'll inject our `greeterService` and the `cbmessagebox` service into the handler by adding these properties to the top of `/handlers/helloWorld.cfc`.  This will put the instance of our services in the variables scope where we can access it in our action methods.

```javascript
component {
	property name='greeterService' inject='greeterService';
	property name='messageBox' inject='messagebox@cbmessagebox';

    ...
}
```

And now in our `index` method, we'll set the output of our service into an `info` message.

```javascript
function index(event,rc,prc){
	messageBox.info( greeterService.sayHello() );
	event.setView("helloWorld/index");
}	
```

One final piece.  Open up the default layout located in `layouts/Main.cfm` and find the `#renderView()#`.  Add this line right before it to render out the message box that we set in our handler.

```html
#getInstance( 'messagebox@cbmessageBox').renderIt()#
<div class="container">#renderView()#</div>
```

Now hit your `helloWorld` handler one final time with `?fwreinit=1` in the URL to see it all in action!  (Again, your port number will most likely be different.

> 127.0.0.1:43272/helloWorld?fwreinit=1

## What's Next?
Congratulations!  In a matter of minutes, you have created a full MVC application.  You installed a community module  from ForgeBox, created a new handler/view and tied in business logic from a service model.  

As easy as that was, you're just scratching the surface of what ColdBox can do for you. Continue reading this book to learn more about:
* Environment-specific configuration
* Easy SES URL routing
* Tons of 3rd party modules
* Drop-in security system
* Sweet REST web service support

If you run in issues or just have questions, please jump on our [ColdBox Google Group](https://groups.google.com/forum/#!forum/coldbox) and ask away.

ColdBox is Professional Open Source under the Apache 2.0 license. We'd love to have your help with the product. 