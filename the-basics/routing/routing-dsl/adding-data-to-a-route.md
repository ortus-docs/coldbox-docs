# Adding Data to a Route

Besides placeholders parsed from the URL itself, you can attach fixed values to the request collection whenever a route matches - useful for flags, defaults, or metadata your handler shouldn't have to guess at.

```javascript
* `rc( name, value, overwrite=true )`        - Add one `RC` value
* `rcAppend( map, overwrite=true )`          - Add multiple `RC` values from a struct
* `prc( name, value, overwrite=true )`       - Add one `PRC` value
* `prcAppend( map, overwrite=true )`         - Add multiple `PRC` values from a struct
```

```javascript
route( "/api/v1/users/:id" )
    .rcAppend( { secured : true } )
    .prcAppend( { name : "hello" } )
    .to( "api-v1:users.show" );
```

`rc` values land in the public request collection (`rc`); `prc` values land in the private collection (`prc`), which is only settable from inside your application - never from user input.
