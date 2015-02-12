# Setting Views

The <code>event</code> object is the object that will let you set the views that you want to render, so please explore its API in the CFC Docs. To quickly set a view to render, do the following:

```js
event.setView('view');
```

The view name is the name of the template in the **views** directory without appending the **.cfm**. So if the view is inside another directory you would do this:

```js
event.setView( 'mydirectory/myView' );
```

We recommend that you set your views following the naming convention of your event.  So if your event is **users.index**, your view should be **users/index.cfm**.  This will go a long way with maintainability and consistency.

