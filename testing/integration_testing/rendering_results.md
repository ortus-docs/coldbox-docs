# Rendering Results

ColdBox offers integration testing out of the box for your entire events and application. However, it does not render the results that would have been produced in a browser from these events unless you tell the test case to do so. The `execute()` method has an argument called renderResults which defaults to false. If you pass in true then ColdBox will go through the normal rendering procedures and save the results in a request collection variable called: cbox_rendered_content. It will even work with `renderData()` or if you are returning RESTful information. Then you can easily assert what the content would have been for an event.

