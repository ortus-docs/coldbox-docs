# Around Advices


<img src="../../images/eventhandler-around.jpg"/>


Around advices are the most powerful of all as you completely hijack the requested action with your own action that looks, smells and feels exactly as the requested action. This will allow you to run both before and after stuff but also surround the method call with whatever logic you want like transactions, try/catch blocks, or even decide to NOT execute the action at all. You can do it globally by using the aroundHandler() method or targeted to a specific action around{actionName}().