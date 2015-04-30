# ColdBox Getting Started Guide

The ColdBox MVC Platform is the de-facto enterprise-level MVC framework for CFML developers.  It's professionally backed, highly extensible, and productive.  Getting started with ColdBox is quick and painless.  The only thing you need to begin is [CommandBox](http://www.ortussolutions.com/products/commandbox), a command line tool for CFML developers.   

## Install CommandBox

You can read through our one-page [CommandBox Getting Started Guide](http://ortus.gitbooks.io/commandbox-documentation/content/getting_started_guide.html).   Or simply grab the CommandBox executable from the [download page](http://www.ortussolutions.com/products/commandbox#download) and double click it to run.  

http://www.ortussolutions.com/products/commandbox

You should now be seeing a prompt that looks like this:

![CommandBox Terminal](../images/commandbox-terminal.png)

## Create A New Site

Now we're cooking with gas!  Let's create new ColdBox site.  CommandBox comes with built-in commands for scaffolding out new sites as well as installing ColdBox and other libraries.  We'll start by changing into an empty directory were we want our new site to live.  If necessary, you can create a new folder.

```bash
CommandBox> mkdir C:\playground
CommandBox> cd C:\playground
```

Now let's ask CommandBox to create a new ColdBox site for us.  The `--installColdBox` flag will also install the latest version of the ColdBox Platform alongside our new app skeleton.