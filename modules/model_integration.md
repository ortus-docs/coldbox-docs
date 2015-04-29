# Models

When you declare a module and you define a `models` folder then the framework automatically register all models in that folder for you using a namespace of `@moduleName`.  This means that all models are registered according to their CFC name plus the namespace.

> **Info** Internally, ColdBox uses WireBox's `mapDirectory()` to map the entire `models` directory for you.
