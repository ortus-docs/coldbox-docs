# Rendering With Local Variables

You can pass localized arguments to the `renderView() and renderLayout()` methods in order to encapsulate the rendering via the `args` argument.  Much like how you make method calls with arguments.  Inside of your layouts and views you will receive the same `args`
Passing local variables to layouts and views: `renderView(args), renderLayout(args)` now get the args argument which can be a structure of data that will be specifically passed to views/layouts for rendering ONLY there. This gives you great DRYness (yes that is a word) when building new and edit forms or views as you can pass distinct arguments to distinguish them and keep structure intact.

#####Universal Form

```js
<h1>#args.type# User</h1>
<form method="post" action="#args.action#">
...
</form>
```

#####New Form
```js
#renderView(view='forms/universal',args={type='new',action='user.create'})#
```

##### Edit Form
```js
#renderView(view='forms/universal',args={type='edit',action='user.update'})#
```


