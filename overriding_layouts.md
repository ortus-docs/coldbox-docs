# Overriding Layouts

Ok, now that we have started to get funky, let's keep going. How can I change the layout on the fly for a specific view? Very easily, using yet another new method from the event object, called `setLayout()`

```js
event.setLayout( name )
```

This is great, so we can change the way views are rendered on the fly programmatically. We can switch the content to a PDF in an instant. So let's do that

```js
