# Creating Your Own Flash Scope

The ColdBox Flash capabilities are very flexible and you can easily create your own Flash Implementations by doing two things:

1. Create a CFC that inherits from `coldbox.system.web.flash.AbstractFlashScope`
2. Implement the following functions: `clearFlash(), saveFlash(), flashExists(), and getFlash()`

## Implementable Methods

| Method | ReturnType | Description |
| --- | --- | --- |
| clearFlash\(\) | void | Will destroy or clear the entire flash storage structure. |
| saveFlash\(\) | void | Will be called before relocations or on demand in order to flash the storage. This method usually talks to the getScope\(\) method to retrieve the temporary flash variables and then serialize and persist. |
| flashExists\(\) | boolean | Checks if the flash storage is available and has data in it. |
| getFlash\(\) | struct | This method needs to return a structure of flash data to reinflate and use during a request. |

> **Caution** It is the developer's responsibility to provide consistent storage locking and synchronizations.

All of the methods must be implemented and they have their unique purposes as you read in the description. Let's see a real life example, below you can see the flash implementation for the session scope:

```javascript
/**
*********************************************************************************
* Copyright Since 2005 ColdBox Framework by Luis Majano and Ortus Solutions, Corp
* www.coldbox.org | www.luismajano.com | www.ortussolutions.com
********************************************************************************
* This flash scope is smart enough to not create unecessary session variables
* unless data is put in it.  Else, it does not abuse session.
* @author Luis Majano <lmajano@ortussolutions.com>
*/
component extends="coldbox.system.web.flash.AbstractFlashScope" accessors="true"{

    // The flash key
    property name="flashKey";

    /**
    * Constructor
    * @controller.hint ColdBox Controller
    * @defaults.hint Default flash data packet for the flash RAM object=[scope,properties,inflateToRC,inflateToPRC,autoPurge,autoSave]
    */
    function init( required controller, required struct defaults={} ){
        super.init( argumentCollection=arguments );

        variables.flashKey = "cbox_flash_scope";

        return this;
    }

    /**
    * Save the flash storage in preparing to go to the next request
    * @return SessionFlash
    */
    function saveFlash(){
        lock scope="session" type="exclusive" throwontimeout="true" timeout="20"{
            session[ variables.flashKey ] = getScope();
        }
        return this;
    }

    /**
    * Checks if the flash storage exists and IT HAS DATA to inflate.
    */
    boolean function flashExists(){
        // Check if session is defined first
        if( NOT isDefined( "session" ) ) { return false; }
        // Check if storage is set and not empty
        return ( structKeyExists( session, getFlashKey() ) AND NOT structIsEmpty( session[ getFlashKey() ] ) );
    }

    /**
    * Get the flash storage structure to inflate it.
    */
    struct function getFlash(){
        if( flashExists() ){
            lock scope="session" type="readonly" throwontimeout="true" timeout="20"{
                return session[ variables.flashKey ];
            }
        }

        return {};
    }

    /**
    * Remove the entire flash storage
    * @return SessionFlash
    */
    function removeFlash(){
        lock scope="session" type="exclusive" throwontimeout="true" timeout="20"{
             structDelete( session, getFlashKey() );
        }
        return this;
    }

}
```

As you can see from the implementation, it is very straightforward to create a useful session flash RAM object.

