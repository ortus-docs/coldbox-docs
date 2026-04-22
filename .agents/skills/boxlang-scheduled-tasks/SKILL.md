---
name: boxlang-scheduled-tasks
description: "Use this skill when creating or managing BoxLang scheduled workloads: Scheduler DSL classes, BaseScheduler/ScheduledTask fluent APIs, schedulerStart/schedulerGet/schedulerStats BIFs, cron expressions, frequency constraints, lifecycle callbacks, or HTTP-driven tasks with the bx:schedule component."
---

# BoxLang Scheduled Tasks

## Overview

BoxLang offers two scheduling models:

1. **Scheduler DSL (in-process code execution)** using scheduler classes backed by `BaseScheduler` and `ScheduledTask`
2. **`bx:schedule` component (HTTP-driven tasks)** for create/update/delete/pause/resume/list/run task endpoints

Use the Scheduler DSL for rich in-process logic and task callbacks. Use `bx:schedule` for simple persistent URL triggers.

## Application.bx Integration

You can register scheduler classes directly in `Application.bx` so they are auto-discovered for the app context.

For full descriptor behavior (app discovery, multi-app isolation, lifecycle), see
[`application-descriptor`](../application-descriptor/SKILL.md).

```boxlang
class {

    this.name = "MyApp"

    // Auto-register scheduler classes for this application
    this.schedulers = [
        "schedulers.DailyMaintenance",
        "schedulers.HourlyReports"
    ]

}
```

Use this when scheduler lifecycle should be managed with application startup/shutdown rather than ad-hoc `schedulerStart()` calls.

## Option 1: Scheduler DSL (Recommended for Code Workloads)

A scheduler class wraps `BaseScheduler` and registers tasks fluently in `configure()`.

```boxlang
class {

    property name="scheduler"
    property name="logger"

    function configure() {
        scheduler
            .setSchedulerName( "reporting-scheduler" )
            .setTimezone( "America/Chicago" )

        scheduler.task( "daily-report", "reports" )
            .call( () => generateDailyReport() )
            .everyDayAt( "02:00" )
            .onFailure( ( task, exception ) => {
                logger.error( "Task #task.getName()# failed: #exception.message#" )
            } )
    }

}
```

### Registering and Starting Scheduler Classes

```boxlang
// Start from class path (creates, configures, registers, and starts)
schedulerStart( className = "schedulers.ReportingScheduler", name = "reporting", force = true )

// Lookup / control
schedulerGet( "reporting" )
schedulerStats( "reporting" )
schedulerRestart( "reporting", force = false, timeout = 30 )
schedulerShutdown( "reporting", force = false, timeout = 30 )
```

## Option 2: `bx:schedule` Component (HTTP-Driven)

`bx:schedule` persists task definitions (default: `${boxlang-home}/config/tasks.json`) and executes scheduled HTTP GET requests.

```boxlang
bx:schedule action="create"
    task="nightlyCleanup"
    url="https://myapp.example/tasks/cleanup"
    cronTime="0 2 * * *";

bx:schedule action="pause" task="nightlyCleanup";
bx:schedule action="run" task="nightlyCleanup";
bx:schedule action="delete" task="nightlyCleanup";
```

Use this approach for webhooks, callback endpoints, and operational pings.

## Scheduler BIFs

Core scheduler lifecycle BIFs:

- `schedulerStart( className, name?, force=true )`
- `schedulerGet( name )`
- `schedulerGetAll()`
- `schedulerList()`
- `schedulerStats( name? )`
- `schedulerRestart( name, force=false, timeout=30 )`
- `schedulerShutdown( name, force=false, timeout=30 )`

Notes:

- `schedulerStart()` instantiates the BoxLang class, wraps it in `BoxScheduler`, calls `configure()`, then registers/starts it.
- `schedulerRestart()` and `schedulerShutdown()` validate scheduler existence and throw if missing.

## Task Registration Patterns

Use meaningful names and optional groups for operational clarity.

```boxlang
scheduler.task( "cleanup-expired-sessions", "maintenance" )
    .call( () => cleanupSessions() )
    .every( 15, "minutes" )

scheduler.task( "send-weekly-digest", "notifications" )
    .call( () => sendDigest() )
    .everyWeekOn( "Monday", "08:30" )
```

Disable-at-registration for debugging:

```boxlang
scheduler.xtask( "experimental-task", "debug" )
    .call( () => runExperiment() )
    .everyMinute()
```

## Common Frequency APIs

- `every( period, timeUnit )`
- `spacedDelay( delay, timeUnit )` (no overlap style)
- `cron( expression )`
- `everySecond()`, `everyMinute()`, `everyHour()`, `everyHourAt( minute )`
- `everyDay()`, `everyDayAt( "HH:mm" )`
- `everyWeek()`, `everyWeekOn( day, time )`
- `everyMonth()`, `everyMonthOn( day, time )`
- `everyYear()`, `everyYearOn( month, day, time )`
- `onWeekdays( time )`, `onWeekends( time )`

Time units supported in scheduler APIs include: `days`, `hours`, `minutes`, `seconds`, `milliseconds`, `microseconds`, `nanoseconds`.

## Cron Support

Supported formats:

- **5-field Unix**: `minute hour day-of-month month day-of-week`
- **6-field Quartz**: `second minute hour day-of-month month day-of-week`

Examples:

```boxlang
// Every day at 2:00 AM
scheduler.task( "nightly" )
    .call( () => nightlyJob() )
    .cron( "0 2 * * *" )

// Every weekday at 8:30:00 AM
scheduler.task( "weekday-digest" )
    .call( () => digestJob() )
    .cron( "0 30 8 * * MON-FRI" )
```

## Best Practices

- Always set explicit scheduler `name` and `timezone`.
- Use descriptive task names and logical groups.
- Keep task bodies small; move heavy fan-out work to async executors.
- Prefer `onFailure` hooks and centralized logging for observability.
- Avoid overly fine-grained intervals without throughput checks.
- Use `spacedDelay()` or no-overlap patterns when tasks can run longer than their interval.

## Troubleshooting

- **Scheduler not found**: confirm `schedulerStart()` was called and `schedulerList()` contains the name.
- **Task never firing**: verify timezone, cron expression, and start/end constraints.
- **Duplicate/stacked runs**: use spacing/no-overlap strategies instead of strict fixed-rate behavior.
- **Silent failures**: inspect scheduler logger output and `schedulerStats()` counters (`totalFailures`, `lastResult`).