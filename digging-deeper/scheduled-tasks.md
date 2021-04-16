# Scheduled Tasks

## Introduction

Scheduled tasks have always been a point of soreness for many developers in _ANY_ language.  Especially choosing where to place them for execution: should it be cron? windows task scheduler? ColdFusion engine? Jenkins, Gitlab? and the list goes on and on.

![ColdBox Scheduled Tasks](../.gitbook/assets/coldboxscheduler.png)

The _ColdBox Scheduled Tasks_ offers a fresh, programmatic and human approach to scheduling tasks on your server and multi-server application.  It allows you to define your tasks in a portable **Scheduler** we lovingly call the `Scheduler.cfc` which not only can be used to define your tasks, but also monitor all of their life-cycles and metrics of tasks.  Since ColdBox is also hierarchical, it allows for every single ColdBox Module to also define a `Scheduler` and register their own tasks as well.  This is a revolutionary approach to scheduling tasks in an HMVC application.

{% hint style="success" %}
The ColdBox Scheduler is built on top of the core async package Scheduler.
{% endhint %}

## Global Scheduler - `appScheduler@coldbox`

Every ColdBox application has a global scheduler created for you by convention.  However, you can have complete control of the scheduler by creating the following file: `config/Scheduler.cfc`.  This is a simple CFC with a `configure()` method where you will define your tasks and several life-cycle methods.

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
| `onAnyTaskError()` | Called whenever ANY task fails |
| `onAnyTaskSuccess()` | Called whenever ANY task succeeds |
| `beforeAnyTask()` | Called before ANY task runs |
| `afterAnyTask()` | Called after ANY task runs |

### Configuration Methods

There are two methods that can alter the tasks registered in the scheduler:

| Method | Description |
| :--- | :--- |
| `setTimezone( timezone )` | Set the timezone to use for all registered tasks |
| `setExecutor( executor )` | Override the executor generated for the scheduler |

#### Timezone For All Tasks

By default, all tasks run under the system default timezone which usually is UTC.  However, if you would like to change to a different execution timezone, then you can use the `setTimeZone()` method and pass in a valid timezone string: 

```javascript
setTimezone( "America/Chicago" )
```

#### Custom Executor

By default the scheduler will register a `scheduled` executor with a default of 20 threads for you with a name of `appScheduler@coldbox-scheduler.`  If you want to add in your own executor as per your configurations, then just call the `setExecutor()` method.

```javascript
setExecutor( 
    getAsyncManager().newScheduledExecutor( "mymymy", 50 ) 
);
```

### Scheduler Properties

Every scheduler has the following injections available to you in the `variables` scope

| Object | Description |
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

