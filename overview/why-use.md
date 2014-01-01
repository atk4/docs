# What is Agile Toolkit?

Agile Toolkit is a PHP framework for development of Web Application. It is inspired by Mobile and Desktop frameworks, but the concept is used for building Native HTML applications. Agile Toolkit has a strong philosophy and is driven by the key 5 principles: Simple, Advanced, Composite, Inherited and Agile.

In terms of features - Using Agile Toolkit will offer you a consistent HTML/CSS-based interface out of the box, objects for UI widgets, Data Modeling tools, support for relational and NoSQL data storages with minimum amount of coding.

Agile Toolkit is entirely driven by PHP language (except for templates) - no need to learn anything new. It has a very low entry barrier for beginners and is a great way to learn most popular web language - PHP.

If you use Agile Toolkit for your project, it will also enforce consistent and readable code, making support and long-term projects highly cost-efficient. When you introduce a new experienced developer to your existing project, he will be able to track down and understand structure of your project almost instantly and perform critical changes without danger of breaking the project.

![What is Agile Toolkit](what-is/what-is.png)

Agile Toolkit comes integrated with www.agiletoolkit.org to further help developers with basic tasks such as getting help, finding and installing add-ons, sharing code and publishing your project to your own free web hosting.

Agile Toolkit framework is dual licensed - AGPL license permits use in personal and open-source projects. Additionally you receive free hosting for your project with no time limitation.

For developers looking to develop closed-source projects and deploy those to their own private servers a paid subscription will be required for the duration of development comparable. This is comparable to the private repository service offered by github.com.

## Comparing Agile Toolkit

### to Xcode / Cocoa for iOS from Apple

Agile Toolkit offers PHP developers a similar experience to Xcode by Apple for developing of iOS application.

* Both are free to download and use
* Both require to subscribe for commercial application deployment
* Both offer sophisticated framework (Cocoa and Agile Toolkit PHP)
* Both focus on providing you, developer, with great tools to help you develop
* Both offer you full-stack solution
* Both allow you to use most of the advanced environment technologies with simple interface.

### to other Open-Source web frameworks

Competing PHP framework offer you partial solution, require much higher knowledge, require you to write more code, offer no user interface, does not encourage you to write Open-Source projects, does not offer free app hosting, offer no community portal yet very expressive commercial support and are inherently designed to be "complex" or "slow" so that the company behind the framework could sell more commercial support / training or enterprise server solutions.

## Why learn Agile Toolkit?

Agile Toolkit can be taught and learned by developers with virtually no experience. Agile Toolkit does not require prior experience or deep understanding. Instead it offers the best academical and commercial value for your time investment.

### for students

PHP Language has a great support for object-oriented development. Universities traditionally use Java or Smalltalk to demonstrate Object Oriented approach, however a popular software in PHP is ofter messy and is based on poor development practices.

Agile Toolkit teaches you to develop an efficient, stable and clean code. It will teach you to be a better architect and structure your application better without distracting you to a less-relevant technologies.

There is no cost to learn Agile Toolkit and you can publish your project to demonstrate it to your teacher. You also do not need to know how to set up your web application - Agile Toolkit does it all for you.

As you graduate, you can use Agile Toolkit for any web project. PHP is the most popular web language and is very widely supported.

### for course attendees

Agile Toolkit courses are available in some countries. Usually those are paid courses but you receive a digital certificate as you complete the course.

### for commercial companies

Most of the Web Frameworks are born from a academical projects or as certain developer migrates from one language to another. The initial structure of those frameworks are simple yet offers you almost NO help. As frameworks mature, they become overcomplicated introduce segmentation and resistance to further refactoring.

Agile Toolkit has undergone a 3 (THREE) major refactoring stages. Each stage was heavily used in hundreds of agile commercial projects with all sorts of requirements. The 4.0 of Agile Toolkit have been released in 2008 and have significantly matured without loosing key benefits.

If you develop your web software (or part of it) using Agile Toolkit - you'll have the most efficient development cycle you have ever experienced. The time you save on project support, component development and bug fixes significantly outweigh your commercial licensing and training costs.

All of the Agile Toolkit codebase is copyright by Agile Toolkit Limited - UK Based commercial company dedicated to growth and improvement of the framework. Agile Toolkit is our bread and butter and is where we will continue to innovate and improve.

### for non-developers

If you are an administrator or only briefly need a framework to build a quick project - Agile Toolkit is ideal for you. From all the frameworks - Agile Toolkit gives you the most with minimum learning, which means your application will be done in no-time.

### for open-source

If you plan to develop a web project - you should consider AGPL license - which is GPL equivalent for web apps and is compatible with MIT-based components.

Agile Toolkit will give you the quickest result and can be easily adopted by any Agile Toolkit developer out there to make contributing to your application super-easy.

## Example: a full-featured CRUD UI in 2 lines of code

To demonstrate the agility of the Agile Toolkit approach, here's how you'd build a fully AJAXed [CRUD](http://en.wikipedia.org/wiki/Create,_read,_update_and_delete) application.  Let's start with the vanilla built-in CRUD component:

    $crud = $page->add('CRUD');
    $crud->setModel('Employee');

That's it &ndash; our page is now displaying an attractive and functional CRUD interface, and enforcing any rules set in our `Employee` Model.

In Agile Toolkit CRUD is just another component, with many powerful and flexible features built-in &ndash; for example:

    $crud->addPaginator(5);
    $crud->addQuickSearch(array('name', 'department'));

Components are easy to extend and customize. CRUD is a composite View relying on Grid, Form and Button Views and we can interact with those subcomponents directly:

    $crud = $this->add('CRUD');
    $crud->setModel('Book');

    if ($crud->grid) {
        if ($crud->grid->addButton('Populate Data')->isClicked()) {
            $crud->grid->model->populateData();
            $crud->grid->js()->reload()->execute();
        }
    }

Now we have a button which uses some AJAX to repopulate the CRUD form with a default data set. As you can see, there's no need to get your hands dirty with the AJAX internals. The actual implementation of `populateData()` is part of the business logic and resides in `Model_Book`.

Let's add a relational join:

    $book = $this->add('Model_Book');
    $author = $book->leftJoin('author');
    $author->addField('author_name','name')->editable(false);

    $crud = $this->add('CRUD');
    $crud->setModel($book);

    if ($crud->grid) {
        if ($crud->grid->addButton('Populate Data')->isClicked()) {
            $crud->grid->model->populateData();
            $crud->grid->js()->reload()->execute();
        }
    }

Our CRUD component is displaying data from two tables. But in Agile Toolkit it would be better style to encapsulate our join in a new Model:

    class Model_BookWithAuthor extends Model_Book
    {
        function init()
        {
            parent::init();
            $author = $this->leftJoin('author');
            $author->addField('author_name','name')->editable(false);
        }
    }

Models in Agile Toolkit are unusually powerful and flexible &ndash; this is merely a first glimpse of how they work.

Finally, you might want your CRUD with the 'Populate Data' button as a reusable component, so let's move it into another new class:

    class MyCRUD extends CRUD
    {
        function render()
        {
            if ($this->grid && $this->grid->model->hasMethod('populateData'))) {
                if ($this->grid->addButton('Populate Data')->isClicked()) {
                    $this->grid->model->populateData();
                    $this->grid->js()->reload()->execute();
                }
            }
            parent::render();
        }
    }

So now we can use our two new components with just 2 lines of code:

    $crud = $this->add('MyCRUD');
    $crud->setModel('BookWithAuthor');

With other solutions similar CRUD functionality might require dozens, even hundreds of lines of code. So Agile Toolkit offers the potential to accelerate your CRUD development by a factor of ten or more, without resorting to inflexible libraries or code generation.

This example gives you a foretaste of some of the most powerful features of Agile Toolkit:

* You can easily enhance existing components and even interact with the subcomponents they're built from
* Code in Models and Views can be quickly refactored to encapsulate new functionality without disrupting existing code and tests
* You can develop and test custom components separately, then combine them into a UI of any complexity
* Components benefit from the built-in CSS styling, providing you with an attractive interface with no additional work.


## Can I Use Agile Toolkit With My Existing Code?

We recognise that many developers will be coming to Agile Toolkit with a significant investment in other MVC frameworks, or in content management frameworks such as WordPress, Drupal and Joomla. 

If you want to add sophisticated data handling to your legacy code, Agile Toolkit is designed to play well with these other frameworks. For example you might use the Agile Toolkit UI features for just a couple of pages inside your CodeIgniter project, or you can use the unique features of the Agile Toolkit ORM to build the REST interface for your  JavaScript frontend. Commonly, developers will use Agile Toolkit to modernise the backend/admin area of an existing project. You'll find more details [here](/TODO).
