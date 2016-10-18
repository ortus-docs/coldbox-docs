# Adding A Model

Let's complete our saga into MVC by developing the **M**, which stands for model.  This layer is all your business logic, queries, external dependencies, etc. of your application, which represents the problem to solve or the domain to solve.  This layer is controlled by WireBox, the dependency injection framework within ColdBox, which will give you the flexiblity of wiring your objects and persisting them for you.

Let's create a simple contact listing, so open up CommandBox and issue the following command:

```bash
coldbox create model name="ContactService" methods="getAll" persistence="singleton"
```

This will create a `models/ContactService.cfc` and a companion unit test at `tests/specs/unit/ContactServiceTest.cfc`.