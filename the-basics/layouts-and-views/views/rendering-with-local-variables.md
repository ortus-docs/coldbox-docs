# Rendering With Local Variables

You can pass localized arguments to the `renderView() and renderLayout()` methods in order to encapsulate the rendering via the `args` struct argument. Much like how you make method calls with arguments. Inside of your layouts and views you will receive the same `args` struct reference as well.

This gives you great DRYness \(yes that is a word\) when building new and edit forms or views as you can pass distinct arguments to distinguish them and keep structure intact.

## Universal Form

```javascript
<h1>#args.type# User</h1>
<form method="post" action="#args.action#">
...
</form>
```

### New Form

```javascript
#renderView(view='forms/universal',args={type='new',action='user.create'})#
```

### Edit Form

```javascript
#renderView(view='forms/universal',args={type='edit',action='user.update'})#
```

