---
name: boxlang-file-watchers
description: "Use this skill when implementing BoxLang filesystem watchers: watcherNew/watcherStart/watcherStop lifecycle BIFs, WatcherInstance APIs, event payload handling, recursive directory monitoring, debounce/throttle/atomicWrites tuning, errorThreshold auto-stop behavior, and listener patterns (closure, struct, class, class name string)."
---

# BoxLang File Watchers

## Overview

BoxLang includes a runtime watcher service for real-time filesystem automation.

Watchers are ideal for:

- hot reload and development workflows
- build pipelines and asset regeneration
- file drop ingestion and ETL triggers
- reacting to create/modify/delete events

## Application.bx Integration

You can register app-scoped watchers directly in `Application.bx` with `this.watchers`.

For full descriptor behavior (app discovery, multi-app isolation, lifecycle), see
[`application-descriptor`](../application-descriptor/SKILL.md).

```boxlang
class {

    this.name = "MyApp"

    watcherListener = new app.listeners.HotReloadListener()

    this.watchers = {
        sourceWatcher : {
            paths : [ expandPath( "./src" ) ],
            listener : watcherListener,
            recursive : true,
            debounce : 250,
            atomicWrites : true,
            errorThreshold : 10
        }
    }

}
```

Supported watcher definition keys commonly include `paths`, `listener`, `recursive`, `debounce`, `throttle`, `atomicWrites`, and `errorThreshold`.

## Event Model

Event kinds:

- `created`
- `modified`
- `deleted`
- `overflow`

Listener payload struct keys:

- `kind`
- `path` (absolute path; blank for overflow)
- `relativePath` (relative to watcher root; blank for overflow)
- `watchRoot` (blank for overflow)
- `timestamp` (ISO-8601)

## Creating a Watcher

`watcherNew()` registers a watcher but does not automatically start it unless you chain `.start()` on the returned instance.

```boxlang
watcher = watcherNew(
    name = "sourceWatcher",
    paths = [ "./src" ],
    listener = ( event ) => {
        println( "[#event.kind#] #event.relativePath#" )
    },
    recursive = true,
    debounce = 250,
    throttle = 0,
    atomicWrites = true,
    errorThreshold = 10,
    force = false
).start()
```

Accepted `paths` forms:

- string path
- array of string paths

## Listener Forms

### 1) Closure / Lambda

```boxlang
listener = ( event ) => {
    if ( event.kind == "modified" ) {
        recompileAsset( event.path )
    }
}
```

### 2) Struct Listener

```boxlang
listener = {
    onCreate: ( event ) => onCreated( event ),
    onModify: ( event ) => onModified( event ),
    onDelete: ( event ) => onDeleted( event ),
    onOverflow: ( event ) => fullResync(),
    onEvent: ( event ) => writeLog( text: "Watcher event: #event.kind#", log: "async" )
}
```

### 3) Class Name String or Class Instance

```boxlang
watcherNew(
    name = "prodWatcher",
    paths = [ "/srv/import" ],
    listener = "app.listeners.ImportWatcher"
)
```

```boxlang
watcherNew(
    name = "prodWatcher",
    paths = [ "/srv/import" ],
    listener = new ImportWatcher()
)
```

For class listeners, implement `onEvent( required struct event )` as the baseline and optionally:

- `onCreate( event )`
- `onModify( event )`
- `onDelete( event )`
- `onOverflow( event )`
- `onError( error )`

## Watcher BIFs

- `watcherNew( name?, paths, listener, recursive=true, debounce=0, throttle=0, atomicWrites=true, delay=0, errorThreshold=10, force=false )`
- `watcherStart( name )`
- `watcherStop( name, force=false )`
- `watcherRestart( name )`
- `watcherGet( name )`
- `watcherGetAll()`
- `watcherList()`
- `watcherExists( name )`
- `watcherShutdown( name )`
- `watcherStopAll( force=false )`
- `watcherShutdownAll( force=false )`

Lifecycle distinctions:

- `stop` = stop execution but keep registration
- `shutdown` = stop + unregister (destructive)

## WatcherInstance API

`watcherNew()` and `watcherGet()` return `WatcherInstance`:

- `start()`
- `stop( force=false )`
- `restart()`
- `isRunning()` / `isStopped()`
- `getState()` / `getStateAsString()`
- `getName()`
- `getWatchPaths()`
- `getListener()`
- `getStats()`

`getStats()` includes:

- `name`
- `state` (`CREATED`, `RUNNING`, `STOPPED`)
- `paths`
- `recursive`
- `debounce`
- `throttle`
- `atomicWrites`
- `errorThreshold`
- `consecutiveErrors`

## Tuning and Safety

- **`debounce`**: suppress rapid duplicate events on the same path.
- **`throttle`**: limit event frequency per path.
- **`atomicWrites`**: reduce noisy editor temp-write sequences.
- **`errorThreshold`**: auto-stop watcher after consecutive listener failures.
- **`force` stop**: faster stop via interruption and watch service close; may drop in-flight events.

## Production Pattern

```boxlang
watcher = watcherNew(
    name = "inbound-files",
    paths = [ "/data/inbound" ],
    listener = new InboundFileListener(),
    recursive = true,
    debounce = 300,
    atomicWrites = true,
    errorThreshold = 5
).start()

if ( !watcher.isRunning() ) {
    watcher.restart()
}
```

## Best Practices

- Keep listener logic fast; offload expensive work to executors/futures.
- Use class listeners for reusable, testable production handlers.
- Always log errors in `onError` and track `consecutiveErrors`.
- Use absolute paths in production to avoid cwd ambiguity.
- Use `watcherStopAll()` in graceful shutdown flows.

## Troubleshooting

- **No events firing**: confirm path exists and is a directory.
- **Too many duplicate events**: increase `debounce`, enable `atomicWrites`, and consider `throttle`.
- **Watcher disappears**: check if `watcherShutdown()` or `watcherShutdownAll()` was invoked.
- **Unexpected stop**: inspect listener exceptions; watcher may have hit `errorThreshold`.