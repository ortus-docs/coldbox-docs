# Layout-View Events

| Interception Point | Intercept Structure | Description |
| :--- | :--- | :--- |
| preLayout | --- | This occurs before any rendering or layout is executed |
| preRender | {renderedContent} | This occurs after the layout+view is rendered and this event receives the produced content |
| postRender | --- | This occurs after the content has been rendered to the buffer output |
| preViewRender | view - The name of the view to render | This occurs before any view is rendered |
| postViewRender | All of the data above plus:  | This occurs after any view is rendered and passed the produced content |
| preLayoutRender | layout - The name of the layout to render | This occurs before any layout is rendered |
| postLayoutRender | Everything above plus:  | This occurs after any layout is rendered and passed the produced content |

