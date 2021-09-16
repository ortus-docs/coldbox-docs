# Scheduled Tasks

## Introduction

Scheduled tasks have always been a point of soreness for many developers in _ANY_ language.  Especially choosing where to place them for execution: should it be cron? windows task scheduler? ColdFusion engine? Jenkins, Gitlab? How do I deal with security? Will it be exposed? and the list goes on and on.

![ColdBox Scheduled Tasks](../.gitbook/assets/coldboxscheduler.png)

The _ColdBox Scheduled Tasks_ offers a fresh, programmatic and human approach to scheduling tasks on your server and multi-server application.  It allows you to define your tasks in a portable **Scheduler** we lovingly call the `Scheduler.cfc` which not only can be used to define your tasks, but also monitor all of their life-cycles and metrics of tasks.  Since ColdBox is also hierarchical, it allows for every single ColdBox Module to also define a `Scheduler` and register their own tasks as well.  This is a revolutionary approach to scheduling tasks in an HMVC application.

{% hint style="success" %}
The ColdBox Scheduler is built on top of the core async scheduler.
{% endhint %}

## Global Scheduler

Every ColdBox application has a global scheduler created for you by convention and registered with a WireBox ID of `appScheduler@coldbox`.  However, you can have complete control of the scheduler by creating the following file: `config/Scheduler.cfc`.  This is a simple CFC with a `configure()` method where you will define your tasks and several life-cycle methods.

{% code title="config/Scheduler.cfc" %}
```javascript
component {

	/**
	 * Configure the ColdBox Scheduler
	 */
	function configure() {
		/**
		 * --------------------------------------------------------------------------
		 * Configuration Methods
		 * --------------------------------------------------------------------------
		 * From here you can set global configurations for the scheduler
		 * - setTimezone( ) : change the timezone for ALL tasks
		 * - setExecutor( executorObject ) : change the executor if needed
		 */
		


		/**
		 * --------------------------------------------------------------------------
		 * Register Scheduled Tasks
		 * --------------------------------------------------------------------------
		 * You register tasks with the task() method and get back a ColdBoxScheduledTask object
		 * that you can use to register your tasks configurations.
		 */
			
		task( "Clear Unregistered Users" )
			.call( () => getInstance( "UserService" ).clearRecentUsers() )
			.everyDayAt( "09:00" );
			
		task( "Hearbeat" )
			.call( () => runEvent( "main.heartbeat" ) )
			.every( 5, "minutes" )
			.onFailure( ( task, exception ) => {
				getInstance( "System" ).sendBadHeartbeat( exception );
			} );
	}

	/**
	 * Called before the scheduler is going to be shutdown
	 */
	function onShutdown(){
	}

	/**
	 * Called after the scheduler has registered all schedules
	 */
	function onStartup(){
	}

	/**
	 * Called whenever ANY task fails
	 *
	 * @task The task that got executed
	 * @exception The ColdFusion exception object
	 */
	function onAnyTaskError( required task, required exception ){
	}

	/**
	 * Called whenever ANY task succeeds
	 *
	 * @task The task that got executed
	 * @result The result (if any) that the task produced
	 */
	function onAnyTaskSuccess( required task, result ){
	}

	/**
	 * Called before ANY task runs
	 *
	 * @task The task about to be executed
	 */
	function beforeAnyTask( required task ){
	}

	/**
	 * Called after ANY task runs
	 *
	 * @task The task that got executed
	 * @result The result (if any) that the task produced
	 */
	function afterAnyTask( required task, result ){
	}

}

```
{% endcode %}

### Life-Cycle Methods

Every Scheduler can create life-cycle methods and monitor the scheduled tasks:

| Method | Description |
| :--- | :--- |
| `onStartup()` | Called after the scheduler has registered all schedules |
| `onShutdown()` | Called before the scheduler is going to be shutdown |
| `onAnyTaskError(task,exception)` | Called whenever ANY task fails |
| `onAnyTaskSuccess(task,result)` | Called whenever ANY task succeeds |
| `beforeAnyTask(task)` | Called before ANY task runs |
| `afterAnyTask(task,result)` | Called after ANY task runs |

### Configuration Methods

| Method | Description |
| :--- | :--- |
| `setTimezone( timezone )` | Set the timezone to use for all registered tasks |
| `setExecutor( executor )` | Override the executor generated for the scheduler |

#### Timezone For All Tasks

By default, all tasks run under the system default timezone which usually is UTC.  However, if you would like to change to a different execution timezone, then you can use the `setTimeZone()` method and pass in a valid timezone string: 

```javascript
setTimezone( "America/Chicago" )
```

{% hint style="success" %}
You can find all valid time zone Id's here: [https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/time/ZoneId.html](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/time/ZoneId.html)
{% endhint %}

#### Custom Executor

By default the scheduler will register a `scheduled` executor with a default of 20 threads for you with a name of `appScheduler@coldbox-scheduler.`  If you want to add in your own executor as per your configurations, then just call the `setExecutor()` method.

```javascript
setExecutor( 
    getAsyncManager().newScheduledExecutor( "mymymy", 50 ) 
);
```

{% hint style="info" %}
You can find how to work with executors in our [executors](promises-async-programming/executors.md) section.
{% endhint %}

### Scheduler Properties

Every scheduler has the following injections available to you in the `variables` scope

| Property | Description |
| :--- | :--- |
| `asyncManager` | Async manager reference |
| `cachebox` | CacheBox reference |
| `controller` | ColdBox controller reference |
| `executor` | Scheduled executor |
| `log` | A pre-configured log object |
| `started` | A boolean flag indicating if the scheduler has started or not |
| `tasks` | The collection of registered tasks |
| `timezone` | Java based timezone object |
| `util` | ColdBox utility |
| `wirebox` | WireBox reference |

### Scheduler ColdBox Methods

Every scheduler has several useful ColdBox interaction methods you can use when registering your tasks callable methods.

| Method | Description |
| :--- | :--- |
| `announce()` | Announce an interception |
| `externalView()` | Render an external view |
| `getCache()` | Get a cache from CacheBox |
| `getColdBoxSetting()` | Get a ColdBox setting |
| `getEnv()` | Retrieve a environment variable only |
| `getInstance()` | Get a instance object from WireBox |
| `getModuleConfig()` | Get a module config |
| `getModuleSettings()` | Get a module setting |
| `getRenderer()` | Get the ColdBox Renderer |
| `getSetting()` | Get an app Setting |
| `getSystemSetting()` | Retrieve a Java System property or env value by name. It looks at properties first then environment variables |
| `getSystemProperty()` | Retrieve a Java System property only |
| `layout()` | Render a layout |
| `locateDirectoryPath()` | Resolve a directory to be either relative or absolute in your application |
| `locateFilePath()` | Resolve a file to be either relative or absolute in your application |
| `runEvent()` | Run a ColdBox Event |
| `runRoute()` | Run a ColdBox Route |
| `settingExists()` | Check if a setting exists |
| `setSetting()` | Set a setting |
| `view()` | Render a view |

### Scheduler Utility Methods

Every scheduler has several utility methods:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Method</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>getRegisteredTasks()</code>
      </td>
      <td style="text-align:left">Get an ordered array of all the tasks registered in the scheduler</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>getTaskRecord( name )</code>
      </td>
      <td style="text-align:left">
        <p>Get the task record structure by name:</p>
        <p><code>{ </code>
        </p>
        <p><code> name, </code>
        </p>
        <p><code> task, </code>
        </p>
        <p><code> future, </code>
        </p>
        <p><code> scheduledAt, </code>
        </p>
        <p><code> registeredAt, </code>
        </p>
        <p><code> error, </code>
        </p>
        <p><code> errorMessage, </code>
        </p>
        <p><code> stacktrace </code>
        </p>
        <p><code>}</code>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>getTaskStats()</code>
      </td>
      <td style="text-align:left">Builds out a struct report for all the registered tasks in this scheduler</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>hasTask( name )</code>
      </td>
      <td style="text-align:left">Check if a scheduler has a task registered by name</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>hasStarted()</code>
      </td>
      <td style="text-align:left">Has the scheduler started already</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>removeTask( name )</code>
      </td>
      <td style="text-align:left">Cancel a task and remove it from the scheduler</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>startup()</code>
      </td>
      <td style="text-align:left">Startup the scheduler. This is called by ColdBox for you. No need to call
        it.</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>shutdown()</code>
      </td>
      <td style="text-align:left">Shutdown the scheduler</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>task( name )</code>
      </td>
      <td style="text-align:left">Register a new task and return back to you the task so you can build it
        out.</td>
    </tr>
  </tbody>
</table>

## Schedulers For Modules

Every module in ColdBox also has a convention of `config/Scheduler.cfc` that if detected will register that scheduler for you with a WireBox ID of `cbScheduler@{moduleName}`.  ColdBox will register the scheduler for you and also store it in the module's configuration struct with a key of `scheduler`.  ColdBox will also manage it's lifecycle and destroy it if the module is unloaded.

```bash
+ MyModule
  + config
     - Router.cfc
     - Scheduler.cfc
```

