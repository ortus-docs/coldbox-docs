# Book

So what can `Book.cfc` do. It can have the following private properties:

* name
* id
* createdata
* ISBN
* author
* publishDate

It can then have getters/setters for each property that I want to expose to the outside world, remember that objects should be shy. Only expose what needs to be exposed. Then I can add extra functionality or behavior as needed. You can do things like:

* have a method that checks if the publish date is within a certain amount of years
* have a method that can output the [ISBN](http://www.amazon.com/exec/obidos/ASIN/) number in certain formats
* have a method that can output the publish date in different formats and locales
* make the object save itself or persist itself \(Active Record\)
* and so much more

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/modelguide_book.png)

Now, all you OO gurus might be saying, why did he leave the author as a string and not represented by another object. Well, because of simplicity. The best practice, or that code smell you just had, is correct. The author should be encapsulated by its own model object Author that can be aggregated or used by the Book object. I will not get into details about object aggregation and composition, but just understand that if you thought about it, then you are correct. Moving along...

> **Important** Your objects are not always supposed to be dumb, or just have getters and setters \(Anemic Model\). Enrich them please!

## Book Service

Back to the book service object. This service will need a datasource name \(which could be encapsulated in a datasource object\) in order to connect to the database and persist stuff. It might also need a table prefix to use \(because I want to\) and it comes from a setting in my application's configuration file. Ok, so now we know the following dependencies or external forces:

* A datasource \(as an object or string\)
* A setting \(as a string\)

I can also now think of a few methods that I can have on my book service:

* `getBook([id:string])`:Book This method will create or retrieve a book by id.
* `searchBook(criteria:string)`:query This method can return a query or array of Books if needed
* `saveBook(book:Book)` Save or Update a book
* `deleteBook(book:Book)` Delete a book

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/modelguide_bookservice.png)

I recommend you model these class relationships in [UML class diagrams](http://www.agilemodeling.com/artifacts/classDiagram.htm) to get a better feeling of the design. Anyways, that's it, we are doing domain modeling. We have defined a domain object called `Book` and a companion `BookService` object that will handle book operations.

Now once you build them and UNIT TEST THEM, yes UNIT TEST THEM. Then you can use them in your handlers in order to interact with them. As you can see, most of the business rules and logic are encapsulated by these domain objects and not written in your event handlers. This creates a very good design for portability, sustainability and maintainability. So let's start actually seeing how to write all of this instead of imagining it. Below you can see a more complete class diagram of this simple example.

![](https://github.com/ortus/coldbox-platform-documentation/tree/24d3f3d16693b36ca41bf5ce0329c6ff33316ef0/images/simplemodelclassdiagram.png)

> **Hint** Note that sometimes the design in UML will not reflect the end product. UML is our guide but not the final product.

