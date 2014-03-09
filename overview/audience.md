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

## Can I Use Agile Toolkit With My Existing Design?

While Agile Toolkit comes with a sophisticated [CSS framework]({page}css{/}), you can always replace standard templates with your own.