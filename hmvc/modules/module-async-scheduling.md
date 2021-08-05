# Module Async Scheduling

Modules can easily tie in to ColdBox's [async scheduling engine](../../digging-deeper/promises-async-programming/scheduled-tasks.md) by defining a `config/Scheduler.cfc` file.  This file only needs a `configure` method.  Inside the file, you can define tasks to run asynchronously on a schedule and ColdBox will take care of the rest.

```javascript
component {

    property name="settings" inject="coldbox:moduleSettings:unleashsdk";
    property name="log" inject="logbox:logger:{this}";

    function configure() {
        task( "unleashsdk-refresh-features" )
            .call( getInstance( "UnleashSDK@unleashsdk" ), "refreshFeatures" )
            .every( variables.settings.refreshInterval, "seconds" )
            .before( function() {
                if ( log.canDebug() ) {
                    log.debug( "Starting to fetch new features from Unleash" );
                } 
            } )
            .onSuccess( function( task, results ) {
                if ( log.canInfo() ) {
                    log.info( "Successfully refreshed features", results );
                }
            } )
            .onFailure( function( task, exception ) {
                if ( log.canError() ) {
                    log.error(
                        "Exception when running task [unleashsdk-refresh-features]:",
                        exception
                    );
                }
            } );

        task( "unleashsdk-send-metrics" )
            .call( getInstance( "UnleashSDK@unleashsdk" ), "sendMetrics" )
            .every( variables.settings.metricsInterval, "seconds" )
            .delay( variables.settings.metricsInterval, "seconds" )
            .before( function() {
                if ( log.canDebug() ) {
                    log.debug( "Starting to send metrics to Unleash" );
                } 
            } )
            .onSuccess( function( task, results ) {
                if ( log.canInfo() ) {
                    log.info(
                        "Successfully sent metrics to Unleash features",
                        results
                    );
                }
            } )
            .onFailure( function( task, exception ) {
                if ( log.canError() ) {
                    log.error(
                        "Exception when running task [unleashsdk-send-metrics]:",
                        exception
                    );
                }
            } );
    }

}
```

