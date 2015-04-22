# Tracing messages to the debug panel

One of the most useful plugins that ColdBox has to offer is the Logger plugin which internally uses [LogBox](http://wiki.coldbox.org/wiki/LogBox.cfm) for all kinds of logging and messaging in your application. It has several functions and utilities that will make your development much much easier. You can see a full listing of this plugin's capabilities [opening the Live API.](http://apidocs.coldbox.org/)

This example deals with how to trace messages to the ColdBox debug panel. You can see a screenshot of how this feature looks below:

![](tracer.png)

As you can see from the screenshot, every tracer message gets display in order and with all the necessary information for you to trace messages. This plugin is very easy to use and the code below explains it all. Have fun using this great plugin and utility:

```js
<---  Create a tracer message --->
<cfset getPlugin("Logger").tracer("Starting dspHello. Using default name")>

<cfscript>
getPlugin("Logger").tracer("method called #mymethod#, arguments #arguments.toString()#);
</cfscript>
```

The last example is very useful, remember the Java core of Coldfusion. You can grab a complex variable and call its java toString() method to deflate the structure, array, etc. Very useful for logging purposes.

> **Important** The tracing methods on ColdBox can persist tracer methods across relocations, until the debugging panel get's rendered. Very useful in ajax or post submittals. 

### LogBox Integration
[LogBox](http://wiki.coldbox.org/wiki/LogBox.cfm) does all the logging and messaging in ColdBox applications. You can actually use LogBox to send messages to the tracer panel from any object in your application via the ColdBoxTracer appender.

Well, this is the end of this easy tutorial, hope you enjoyed it.

