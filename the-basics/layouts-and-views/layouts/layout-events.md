# Layout Events

All rendered layouts have associated events that are announced whenever the layout is rendered via ColdBox Interceptors. These are great ways for you to be able to intercept when layouts are rendered and transform them, append to them, or even remove content from them in a completely decoupled approach. The way to listen for events in ColdBox is to write Interceptors, which are essential simple CFC's that by convention have a method that is the same name as the event they would like to listen to. Each event has the capability to receive a structure of information wich you can alter, append or remove from. Once you write these Interceptors you can either register them in your Configuration File or programmatically.

| Event | Data | Description |
| :--- | :--- | :--- |
| `preLayoutRender` | layout - The name of the layout to render | Executed before any layout is rendered |
| `postLayoutRender` | All of the data above plus:  | Executed after a layout was rendered |

> **Caution** You can disable the layout events on a per-rendering basis by passing the `prePostExempt` argument as true when calling `renderLayout()` methods.

