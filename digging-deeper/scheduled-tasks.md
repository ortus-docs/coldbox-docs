# Scheduled Tasks

## Introduction

Scheduled tasks have always been a point of soreness for many developers.  Especially choosing where to place them for execution: should it be cron? windows task scheduler? ColdFusion engine? Jenkins, Gitlab? and the list goes on and on.

![ColdBox Scheduled Tasks](../.gitbook/assets/coldboxscheduler.png)

The _ColdBox Scheduled Tasks_ offers a fresh, programmatic and human approach to scheduling tasks on your server and multi-server application.  It allows you to define your tasks in a portable **Scheduler** we lovingly call the `Scheduler.cfc` which not only can be used to define your tasks, but also monitor all of their life-cycles and metrics.  Since ColdBox is also hierarchical, it allows for every single module to also define a `Scheduler` and register their own tasks as well.  This is a revolutionary approach to scheduling tasks in an HMVC application.

### Global Scheduler  

