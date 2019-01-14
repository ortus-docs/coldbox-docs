# Overriding Layouts

Ok, now that we have started to get funky, let's keep going. How can I change the layout on the fly for a specific view? Very easily, using yet another new method from the event object, called `setLayout()` or the `layout` argument to the `setView()` method.

```javascript
event.setLayout( name );
event.setView( view="", layout=name );
```

This is great, so we can change the way views are rendered on the fly programmatically. We can switch the content to a PDF in an instant. So let's do that

```javascript
function home(event,rc,prc){

    if( event.valueExists('print') ){
        event.setLayout('layout.PDF');
    }

    // logic here

    // set view
    event.setView('general/home');

}
```

