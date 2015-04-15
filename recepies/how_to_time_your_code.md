# How to time your code

### Introduction

We all know that timing code is needed for debugging and optimization purposes. ColdFusion already implements this with the CFTI[](http://livedocs.adobe.com/coldfusion/7/htmldocs/wwhelp/wwhimpl/common/html/wwhelp.htm?context=ColdFusion_Documentation&file=00000344.htm)MER tag. I use this a lot. However, most of the time I do not have the coldfusion debugging turned on, but I do have my ColdBox debugging panel on. So what do I do to time code execution. Well, the timer plugin. Any timers that are detected via the plugin will be shown in the debugging information panel in the color green. Please look at the [Timer Plugin API](http://www.coldbox.org/api)

![](timermonitor.png)

### Usage

The Timer plugin can be used in three ways:

Using the logTime() method, this requires for you to time the execution and pass the results to the timer.

```js
<---  Start timing using the getTickCOunt method() --->
<cfset stime = getTickcount()>

<---  All the code I want to time below --->
...
...

<---LLog Total Time with plugin --->
<cfset getPlugin("Timer").logTime("My Label",totaltime)>
```

#### start() stop() Method

> **Important** Just make sure that the labels are the same for start and stop.

```js
<---  Start Timer --->
<cfset getPlugin("Timer").start("MyLabel")>

<---  All the code I want to time below --->
...
...

<---  Stop & Log timer --->
<cfset getPlugin("Timer").stop("MyLabel")>
```
### Conclusion

And there you go. You can now time code executions with ease using either the timer plugin. Helping developers code with ease.




