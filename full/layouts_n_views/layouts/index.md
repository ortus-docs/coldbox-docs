# Layouts

![](/images/LayoutViewCombinations.png)

A layout is simply an HTML file that acts as your shell where views can be rendered in and exists in the `layouts` folder of your application. The layouts can be composed of multiple views and one main view. The main view is the view that gets set in the request collection during a request via `event.setView()` usually in your handlers or interceptors. 

You can have as many layouts as you need in your application and they are super easy to override or assign to different parts of your application. Imagine switching content from a normal HTML layout to a PDF or mobile layout with one line of code. How cool would that be? Well, it's that cool with ColdBox. Another big benefit of layouts, is that you can see the whole picture of the layout, not the usual cfmodule calls where tables are broken, or divs are left open as the module wraps itself around content. The layout is a complete html document and you basically describe where views will be rendered. Much easier to build and simpler to maintain.

> **Info** Another important aspect of layouts is that they can also consume other layouts as well. So you can create nested layouts very easily via the `renderLayout()` method.
