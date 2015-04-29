# Async Announcements

The first case involves where you want to completely detach an interception call into the background. You will accomplish this by using the `async=true` argument. This will then detach the execution of the interception into a separate thread and return to you a structure containing information about the thread it created for you. This is a great way to send work jobs, emails, processing and more to the background instead of having the calling thread wait for execution.

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, async=true);
```

### Configuration Arguments

You can also combine this call with the following arguments:
* asyncPrirority : The priority level of the detached thread. By default it uses normal priority level 

```js
var threadData = announceInterception(state="onPageCreate", interceptData={}, async=true, asyncPriority="low");
