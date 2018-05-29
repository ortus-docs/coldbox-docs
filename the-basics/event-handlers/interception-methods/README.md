# Interception Methods

![](/full/images/eventhandler-prepost.jpg)

There are several simple implicit [AOP](http://en.wikipedia.org/wiki/Aspect-oriented_programming) \(Aspect Oriented Programming\) interceptor methods, usually referred to as **advices**, that can be declared in your event handler that the framework will use in order to execute them **before/after** and **around** an event as its fired from the current handler. 

This is great for intercepting calls, pre/post processing, localized security, logging, RESTful conventions, and much more. Yes, you got that right, [Aspect Oriented Programming](http://en.wikipedia.org/wiki/Aspect-oriented_programming) just for you and without all the complicated setup involved! If you declared them, the framework will execute them.

| **Interceptor Method** | **Description** |
| --- | --- |
| `preHandler()` | Executes **before** any requested action \(In the same handler CFC\) |
| `pre{action}()` | Executes **before** the `{action}` requested ONLY |
| `postHandler()` | Executes **after** any requested action \(In the same handler CFC\) |
| `post{action}()` | Executes **after** the `{action}` requested ONLY |
| `aroundHandler()` | Executes **around** any request action \(In the same handler CFC\) |
| `around{action}()` | Executes **around** the `{action}` requested ONLY |

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/eventhandler-around.jpg)

