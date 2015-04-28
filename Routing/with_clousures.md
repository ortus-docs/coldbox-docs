# With Clousures

We have created some cool context methods to allow for the prefixing of any of the `addRoute()` arguments by using what we call **with** closures. This allows you to prefix repetitive patterns in route declarations. The best way to see how it works is by example:

```js
addRoute(pattern="/news", handler="public.news", action="index");
addRoute(pattern="/news/recent", handler="public.news", action="recent");
addRoute(pattern="/news/removed", handler="public.news", action="removed");
addRoute(pattern="/news/add/:title", handler="public.news", action="add");
addRoute(pattern="/news/delete/:slug", handler="public.news", action="remove");
addRoute(pattern="/news/v/:slug", handler="public.news", action="view");
```

As you can see from the routes above, we have lots of repetitive code that we can clean out. So let's look at the same routes but using some nice with closures.

```js
with(pattern="/news", handler="public.news")
  .addRoute(pattern="/", action="index")
  .addRoute(pattern="/recent", action="recent")
  .addRoute(pattern="/removed", action="removed")
  .addRoute(pattern="/add/:title",  action="add")
  .addRoute(pattern="/delete/:slug", action="remove")
  .addRoute(pattern="/v/:slug", action="view")
.endWith();
```

As you can see, we start our URL mapping DSL with the *with()* method and pass in any argument the *addRoute()* method declares. In this case we pass a pattern and a handler. Meaning any *addRoute()* method that is concatenated in the with closure will be prefixed with that pattern and handler. Once we concatenate the last *addRoute()*, then we finalize the closure with the .*endWith()*; demarcation. BOOM! The patterns look so much manageable and declarable. The arguments you can use for prefixing or defaulting are:

|Argument|Description|
|--|--|
|pattern|The URL pattern prefix|
|handler|The handler prefix|
|action|The action prefix|
|matchVariables|The match variables prefix|
|view|The view prefix|
|constraints|The constraints to prefix|
|module|The module prefix|
|namespace|The namespace prefix|

> **Important** It is extremely important that you close the with closures with an endWith() call or all subsequent addRoutes() calls, will be using the last with closure you declared. 

