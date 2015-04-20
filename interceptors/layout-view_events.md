# Layout-View Events

|Interception Point|Intercept Structure|Description|
|--|--|--|
|preLayout|---|This occurs before any rendering or layout is executed|
|preRender | {renderedContent}|This occurs after the layout+view is rendered and this event receives the produced content|
|postRender |---|This occurs after the content has been rendered to the buffer output|
|preViewRender |<ul><li>view - The name of the view to render</li><li>cache - If the view will be cached</li><li>cacheTimeout - The cache timeout</li><li>cacheLastAccessTimeout - The idle timeout of the view</li><li>cacheLastAccessTimeout - The idle timeout of the view</li><li>cacheSuffix - A suffix to append to the cacheable key name</li><li>module - The module name of the view if any</li><li>args - The arguments to pass into the view</li><li>collection - The collection this view will iterate on</li><li>collectionAs - The alias of the collection name</li><li>collectionStartRow - The start row of the collection iteration</li><li>collectionMaxRows - The max rows of the collection iteration</li><li>collectionDelim - The delimiter used for the collection iteration </li></ul>|This occurs before any view is rendered|
|postViewRender |All of the data above plus: <ul><li>
    renderedView - The view contents that was rendered</li><ul>|This occurs after any view is rendered and passed the produced content|
|preLayoutRender |<ul><li>layout - The name of the layout to render</li><li>view - The name of the view to render if any</li><li>module - The module name of the layout if any</li><li>args - The arguments to pass into the layout + view (if any)</li><li>viewModule - The name of the module the view will be rendered from</li></ul>|This occurs before any layout is rendered|
|postLayoutRender | Everything above plus: <ul><li>
    renderedLayout - The layout + view contents that was rendered </li></ul>|This occurs after any layout is rendered and passed the produced content|