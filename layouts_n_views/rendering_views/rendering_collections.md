# Rendering Collections

You have a few arguments in the `renderView()` method that deal with collection rendering. Meaning you can pass any array or query and the Renderer will iterate over that collection and render out the view as many times as the records in the colleciton.

* `collection` : A data collection that can be a query or an array of objects, structs or whatever
* `collectionAs` : The name of the variable in the variables scope that will hold the collection pivot.
* `collectionStartRow` : Defaults to 1 or your offset row for the collection rendering
* `collectionMaxRows` : Defaults to show all rows or you can cap the rendering display
* `collectionDelim` : An optional delimiter to use to separate the collection renderings. By default it is empty.

Once you call `renderView()` with a collection, the renderer will render the view once for each member in the collection. The views have access to the collection via arguments.collection or the member currently iterating. The name of the member being iterated as is by convention the same name as the view. So if we do this in any layout or simple view:

```js
#renderView(view='tags/comment',collection=rc.comments)#
```

Then the *tags/commen*t will be rendered as many times as the collection *rc.comments* has members on it and by convention the name of the variable is comment the same as the view name. If you don't like that, then use the collectionAs argument:

```js
#renderView(view='tags/comment',collection=rc.comments,collectionAs='MyComment')#
```

So let's see the collection view now: 

```js
<h1>Title: #comment.getTitle()# (#_counter# of #_items#</h1>
<p>Author: #comment.getAuthor()#</p>
#comment.getComment()#
<hr/>
```

You can see that I just call methods on the member as if I was looping (which we are for you). But you will also see two little variables here: 

* _counter : A variable created for you that tells you in which record we are currently looping on
* _items : A variable created for you that tells you how many records exist in the collection

This will then render that specific dynamic HTML view as many times as their are records in the rc.comments array and concatenate them all for you. In my case, I separate each iteration with a simple <hr/> but you can get fancy and creative.

```js
#renderView(view="home/news", collection=prc.news, collectionStartRow=11, collectionMaxRows=20)#
```


