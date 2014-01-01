# A Fresh Approach To MVC

This document explains the MVC paradigm used in Agile Toolkit to a developer already familiar with MVC Frameworks.

## The Agile Toolkit Approach To MVC

Agile Toolkit has refactored the MVC pattern to better meet the realities of rich application development. We take a highly opinionated approach to MVC, building it into the object structure of the framework. Every class you write (excluding Exceptions) is descended from an `AbstractModel` class, an `AbstractView` class, or an `AbstractController` class. So everything in your application is either a Model, a View or a Controller. 

In contrast to many frameworks, Models, Views and Controllers are all PHP-based classes with powerful built-in functionality of `AbstractObject` designed to be extended, overridden or encapsulated.

Each of these components has a well-defined role, so in Agile Toolkit it's usually pretty clear what should go where. This architectural consistency helps when working with teams or sharing code with the community.

But it's important to note that the division of labor between Models, Views and Controllers is different from the typical MVC framework. 

## What Is a Model In Agile Toolkit?

In Agile Toolkit a Model represents a logical business entity such as `User`, `Payment`, `Book` or a sub-set of an existing Model such as `User_Admin`, `Payment_Pending` or `Delisted_Book`. Models are implemented as classes and inheritance is strongly encouraged. 

A Model encapsulates: 

* The data structure of an entity
* The business rules and logic specific to that entity
* Persistent external storage, including single and multi-record data access.

Models can be manipulated within the application or they can be supplied to Views for user editing. For example, by adding `Model_User` to a `CRUD` View, you can easily create an admin page for editing your system users.

`Model` is the Agile ORM (object relational manager). It provides all the features you've come to expect from a heavyweight PHP ORM, such as data definitions, business rules, built-in query methods, joins, caching and record lifecycle events. But you'll be pleasantly surprised by the simplicity, flexibility and efficiency with which this is achieved.

Models can be related. For example: `Model_Book` may contain a field `author_id` which references `Model_Author`. A view such as `Form` will automatically offer a drop-down (or auto-complete) for selecting a relevant entry from the related Model. 

Add-ons such as `filestore` can introduce new field classes such as `Field_Image`, capable of storing images with support of upload, cropping, thumbnails, meta-information storage and physical storage such as filesystem or Amazon S3.

By inheriting models you can reconfigure your field definitions, conditions and relations. Models can be used to deal with evolving use-cases without refactoring your Presentation Logic or automated tests. For new functionality, extend your existing Model and you avoid code duplication (DRY) without affecting your existing codebase.

In addition:

- Agile Toolkit's [DSQL Query Builder](/TODO) class gives safe and elegant access the full power of your chosen RDBMS, supporting subqueries, expressions, complex joins and multi-step transactions
- Definition of rules and normalizations is exceptionally easy and powerful
- You can work with both single records and multi-record sets
- Joins are used for efficiently, avoiding extra SQL queries.

The combination of Model features and ability to inject reusable business logic into Models with Controllers (through hooks and dynamic methods) helps you to ensure that your business model will remain lean, agile and reliable.

## What Is A Page In Agile Toolkit?

Agile Toolkit offers a special type of View called a Page which organizes the response to a particular URL. In desktop frameworks an Agile Toolkit Page would often be called a 'Presenter'.

## What Is A View In Agile Toolkit?

In Agile Toolkit, you can think of a View as a UI widget or component (also called Helper in some frameworks). A View can appear anywhere: inside another View, on a particular page or on multiple pages. 

### View Composition

Views can be composed out of other views. For example:

- A button is a simple View with custom template.
- `Form` is a good example of a composite View, built from Fields, Buttons and some other views.

You can add Views freely to Pages or to other Views and they will never conflict visually or logically.

A typical page in Agile Toolkit would be composed from 5-20 Views. Despite the number, each View contains its own template and the rendering overhead is minimal.

View initialization and rendering can be transparently cached.

### Javascript and AJAX

Any view in the system has support for JavaScript events and can perform AJAX actions. All the tiresome AJAX plumbing is handled for you under the hood, and everything simply works. For example:

- **Event binding**: you can bind JavaScript events to Views using simple PHP: `$button->js('click', $form->js()->submit());` - clicking a button will submit a form.
- **AJAX reloading**: any view can be 'reloaded' through AJAX without any extra effort, refreshing it's content from the backend.

Agile Toolkit is smart enough to handle the interactions between your interface objects with minimal developer input:

    // Show a flyout message when file upload completes

    $upload = $form->addField('upload','my_file');
    $flyout = $this->add('View_Flyout')->set('Upload Complete');
    
    $upload->js('completed', $flyout->showJS());

This also applies when you're composing complex widgets. For example, `Paginator` in `Grid` is implemented as a separate View and enables AJAX communication between the browser and Grid as you paginate. You don't need to teach Grid how to communicate with `Paginator` &ndash; Agile Toolkit does it for you.

    $grid->addPaginator(5);

Even if you're a JavaScript guru willing to work directly with widgets such as jqGrid, Agile Toolkit can still help. You can encapsulate your JavaScript dependencies into a PHP object allowing any user from your team or the Agile Toolkit community to simply `add()` your grid anywhere as a drop-in replacement for the built-in Toolkit grid. 

### Other View features

In addition, the design of Agile Toolkit Views offer the developer:

- **Automatic rendering**: Pages render automatically, and composite Views render their children recursively
- **Extensibility**: Views are objects &ndash; you can create your own reusable widgets or extend existing ones
- **Encapsulation**: Views encapsulate their own templates, which keeps your HTML clean from code
- **Presentational flexibility**: you can inject custom HTML into a View template without redefining it
- **Layout customization at runtime**: Views can be deleted or moved through simple PHP code at runtime or by controllers.
- **Integration**: Views can depend on additional JavaScript or CSS libraries and Agile toolkit will ensure they are loaded on-demand
- **Plugability**: Addons offer fully styled and customizable Views ranging from simple widgets such as an Autocomplete Field up to complex applications such as a Visual Form Designer or a Content Management System.

Naturally, you can bind a Model to a data-aware View component with a single line of code, and the View will adapt to its structure.

    $grid->setModel('Product');

And finally, Views are automatically styled with an AgileToolkit CSS framework based on your individual theme setting.

## What Is A Controller In Agile Toolkit?

In most MVC frameworks, Controllers organize the response to HTTP requests. As we've seen, in Agile Toolkit this is handled by Pages.

So it's important to understand that Controllers in Agile Toolkit are similar to PHP Traits: they can be added to Models and Views to extend their functionality.

For example you can add Controllers to the API to provide global functionality:

- `Logger`: is added into `Application` to provide more powerful exception reporting and logging tools
- `DB`: Provides connectivity with databases.

Or you can use controllers in one or more Views and Models:

- `Order`: allows you to re-arrange child elements
- `ModelAudit`: enables soft-delete, created datetime and updated datetime inside your Model.

Uses for Controllers include:

* **Pluggable business logic**, such as shipping cost calculators
* **Wrappers** for external APIs such as email broadcasting, social networks or payment gateways
* **Service classes** for coordinating complex interactions between multiple Models
* **Utility classes**, such as libraries for encryption and date manipulation
* **Object configurators**, such as a class to add standard audit fields to Models.

The only limit is your imagination. 

## A Fresh Approach To Addons

A critical aspect of an MVC architecture is the ability to plug in additional View and Model components of any complexity.

Here too Agile Toolkit takes a fresh approach aimed, as always, at improving life of a developer.

With mainstream PHP frameworks the recent emphasis has been on decoupling functionality into standalone [PSR-compliant](https://github.com/php-fig/fig-standards/tree/master/accepted) libraries. This eventually leads to code duplication, diverse implementation and incompatibility between features.

By contrast, Agile Toolkit Addons are designed to rely on Agile Toolkit base classes. They will normally offer new Model and View classes and are highly coupled to the Toolkit so they enjoy seamless access to all the Core features.

Now that Addons no longer require to bundle it's own HTML, CSS, frameworks or basic features, they contain much less code. In contrast to Zend Framework each add-on would have almost no dependencies. 

There's a growing ecosystem of Agile Toolkit Addons and your install will offer a native user interface to browse and install Addons from either packagist.org or agiletoolkit.org.
