# Non-Relational Models > Introduction to Model
----

Base Class for your Real-Life entities. For use with no-SQL Data Controllers. Implements active record. Compatible with most Views.

    $book = $this->add('Model_Book')
      ->setSource('MemCached','book')
      ->load($_GET['id']);
    $book['views']++;
    $book->save();

Model is a fundamental class in Agile Toolkit which introduces concept of a very basic Active Record mechanism. Models also hold meta-information about your entity fields such as types, captions, validation, etc

Depending on the requirement you can choose from 3 parent classes: `Model_Table`, `Model` or `AbstractModel`:

- `Model_Table` is the most commonly used and offers powerful [ORM](http://en.wikipedia.org/wiki/Object-relational_mapping) features for working with relational databases
- `Model` provides a pluggable interface to NoSQL data storage, Arrays and Sessions
- `AbstractModel` is a minimal Model used to develop alternative implementations.


### GOALS AND FEATURES OF MODEL
* **Hierarchical Business Model** - Objects in our life are hierarchical. Your database is not. Agile Toolkit models follow your business model and adapt your data-base.
* **Field Object** - Each field is defined through a "Field" object. Need more meta-information, properties or flexibility? Extend the Field class.
* **Database, Table, ID** - Models assume that controller is using one database at a time. Inside a database you may have multiple tables (or files). Inside each file you will have records identified by "ID" (or key).
* **Allow do modify existing Model** - After you create instance of a model, you can define few more fields if you need to.
* **Multiple Data Controllers** - You may load model through one Data Controller, save will use same controller by default. You can, however, use another controller with same model.
* **Great Syntax** - Models implement iterators and array access. That means you can access model properties as it would be array and you can traverse through records inside a `foreach()` loop.
* **Field names are unique** - You can't have multiple fields with the same name.
* **Use in View or directly** - Models are great for either direct use or to be used by standard views. Some other controllers will accept models too.
* **Hooks** - Models use hooks allowing you to control loading / saving events.

### WHAT DSQL IS NOT?
In the spirit of Agile Toolkit — all of it's classes are strictly focused on a one simple task, without trying to do everything. Here are things which Model will not do and why:

* **No code generation** - Model is implemented in under 200 lines of PHP code. It is designed to be common lowest denominator for many views.
* **No Relations, Dependencies, Conditions or Expressions** - Model is designed to be simple. It does not support RDBMS features.
* **No emulation of SQL** - Model does not attempt to emulate SQL features such as offsets. That's job of a Controller to implement.