# Core > Working With Objects
<!-- Reviewed by Romans: 1 April -->

## Overview

In this article we cover the fundamental principles for working with objects in Agile Toolkit. The techniques are straightforward for UI developers (Desktop / Mobile), but might be  unfamiliar to most developers. 

If you are a web developer and have been using some other web frameworks, open a web page and imagine that instead of HTML elements it consists of web objects. Menu would be one object. All forms could be a special form object. Text is yet another object. Finally all of the page is also an object.

Principle of presenting Web UI through a set of flexible objects is a fundamental concept in Agile Toolkit. There are however invisible objects too. When form is being submitted, it communicates with a Model object. This object tells form which field to add and where to store data. 

This tree of objects in Agile Toolkit is called [Runtime Object Tree](/TODO).

## Direct Adding Of Objects

For now - forget about using the `new` statement and instead let's look at how you create new objects in Agile Toolkit:


```
$form_object = $page->add('Form');
```

This code:

1. Creates a new object of specified class
1. Registers `$form_object` as element within `$page`
1. Returns an instance of the new object.

(Actually there are many more things happening here, but we will look into them later)

A newly created object becomes a part of the existing - extending it through our Composability principle.

## Indirect Adding Of Objects 

Objects may deﬁne wrapper methods for adding certain types of object &ndash; this [syntactic sugar](http://en.wikipedia.org/wiki/Syntactic_sugar) helps keep code clean and expressive. For example: 

- `Form` has a method called `addField()`
- `Grid` has a method `addButton()`;

In addition to calling `add()`, these methods often take arguments which save you from chaining calls:

    $form->addButton('Click Me'); 

instead of a longer and more complex call using add() method:

    $form->add('Button',null,'form_buttons')->setLabel('Click Me');

Some methods also allow you to omit part of the class prefix:

    $form->add('Field_Line','name');
    $form->addField('Line','name');  // use this!

In most cases use of wrapper (`addField`) is the best approach.

## Adding Existing Objects

If an object have already been added, you can move it into a new parent by passing it as ﬁrst argument of an `add()` call.

Some objects may expect to reside within objects of a certain type. Adding paginator inside `Grid` and then moving it into other object might have some unpredictable effects.

It's not a goal of Agile Toolkit to shield itself from obviously improper use, that's why as a developer you must understand core principles and remember which things you shouldn't do.

    $form = $page->add('Form');
    $field = $form->addField('line','foo');

    $columns = $form->add('Columns'); // Adds grid-system based columns 50/50
    $left=$columns->addColumn(6);
    $right=$columns->addColumn(6);

    $right->add($field); // Moves field inside right column

## Adding Models with setModel()

Model can be associated with one or several objects by using setModel(). If you specify a class name instead of the object, it will add a new model object.

All objects have the `setModel()` method, however different objects may implement it differently. Associating Model with page will fill-in data into your page template. Associating same model with Grid will list entries and populate columns.

Once you associate the model, you can access it through `model` property: 

    $grid = $page->add('Grid'); 
    $form = $page->add('Form'); 

    $grid->setModel('User');        // Uses class Model_User 
    $form->setModel($grid->model);  // Reuses the same model object
    
Second and further arguments of `setModel()` can vary from object to object. For example - `Grid` allow you to specify list of fields as a second argument which will be used as columns. `CRUD` object is similar but it accepts two parameters - columns for viewing and then columns for editing. First argument is always either a class name or an existing model object.

## Adding Controllers with setController()

In Agile Toolkit one object can have multiple controllers. They enhance the workings of your object. While in most cases using `add('Controller_Foo')` is sufficient, sometimes object will expect you to call `setController('Foo')`. This method will initialize `controller` property and possibly perform more actions. You should refer to individual object documentation to see if they have any special types of controllers they support.

## Chaining Object Methods

Because a call to add() will often see the following syntax in the application:

    $page->add('Button')->setLabel('Click Me');

In the true spirit of jQuery, most object methods will return a reference to themselves ($this) so you can continue to chain:

     $this->add('FormAndSave')
         ->setModel($model)
         ->loadData($this->api->auth->get('id'));

When you add methods to classes, try to return $this to make it possible to chain your methods.

## Accessing Added Objects

`AbstractObject` provides several handy methods for accessing objects you have added into a parent object:

    $view = $page->add('View','myview');

    $page->hasElement('myview');    // Returns $view or false
    $page->getElement('myview');    // Returns $view or exception
    
Models are typically not referenced directly through the array of elements. This is done to enable PHP garbage collection to remove model when you no longer need it:

    $model = $page->add('Model_Book');
    unset($model);                  // Will destroy $model
    
    $view = $page->add('View');
    $view->destroy();               // Removes object from parent
    unset($view);                   // Will destroy $view

You don't need to call unset() if $view/$model is a local variable inside your method or if you are using it for something else.
        

## Objects With Global Scope

Instead of using GLOBAL scope, Agile Toolkit allows any object ability to access the Application class through `api` property. If you want your object to be universally accessed from any object, link it with the Application class. This pattern is very similar to how plugins work in jQuery.

Here's a simple Agile Toolkit app:

    include 'atk4/loader.php';

    // Create the API object
    $api = new ApiFrontend(); 

    // Every object can access the API through the $api property

    $my_object = $api->add('MyClass');
    $my_object->api === $api;            // is true
    $my_object->api->url('login');       // global api method

    // Every object can use any class added to the API

    $api->myclass = $api->add('MyClass2');
    $my_object->api->myclass->doFoo();

## Initializing Objects 

In Agile Toolkit, we don't initialize objects with PHP's `__construct()` method. Instead, when you add an object Agile Toolkit will automatically execute an `init()` method in the new object. 

This allows us to initialize the object after its `owner`, `api` and `name` properties are already set. For views their `template` will also be parsed before init(). 

Here is a short code from the password Strength Checker indicator. It is there to make sure that you only add StrengthChecker into password field.

    class StrengthChecker extends View {
         function init(){
              parent::init();
               if(!$this->owner instance_of Form_Field_Password)
               {
                   throw $this->exception('Must be added into Password field');
               }
               // ....
        }
    }

### init(), setModel() and render()

As you work with Agile Toolkit, often you will wonder - where you should place your code. Keep the following things in mind:

1. If code is for adding more sub-elements through composability, place it inside init()
2. If code needs to iterate through model data, place it inside render()
3. If code needs to add more sub-elements but must access database or model structure for it - place it inside setModel()

You must keep in mind that there is high probability that `init()` would be called, but `render()` wouldn't - this happens when part of your page is being redrawn. If you are executing an expensive CURL request, certainly try to put it inside `render()`.

### Default Properties

Similarly to how you can specify array of properties to jQuery UI widgets (`$('#dialog').dialog({modal:true})`), in Agile Toolkit you can also pass properties into a new object:

    $password->add(
        'StrengthChecker',
        array('default_text'=>'Secure Password Please!')
    );

Other objects will let you change properties through a special wrapper:

    $password->add('StrengthChecker')
        ->setDefaultText('Secure Password Please');
        
You should always use wrapper if it's available, otherwise use default property. Wrappers are also more universal, because when some other object consists of many sub-objects, you can still use wrappers for it:

    $form->setModel('User');
    $form->getElement('email')->setCaption('Your Email');

Properties on other hand are very useful for overriding class names before object are created:

    // Use standard CRUD with custom Grid
    $page->add(
        'CRUD',
        array('grid_class'=>'MyGrid')
    )->setModel('User'); 
    
## Cloning Objects & newInstance()

From the moment object is created, it may be changed in many different ways. For example, you can take your regular model and add a new `join` to it before using it with a list:

    $book = $this->add('Model_Book');
    $author_join = $book->leftJoin('author');
    $author_join->addField('name')->type('readonly')->caption("Author's Name")

    // Now you can use this model inside a Grid and it will show author name
    // for each book
    
    $this->add('Grid')->setModel($book);

By the time you pass the `$book` to a Grid, not only it would have a standard fields, but one additional field from a related join.

### How to use newInstance()

If you call `$book->newInstance()` it will create a new object of a same class (`Model_Book`). The new instance, however, will not have author join. This can be useful in some cases when you want to work with a copy of a model:

    function duplicate()
    {
        return $this->newInstance()->set($this->get())->save();
    }

The above example implements a generic duplication method. It will create a new model of a same class, copy data from the current model into new one and call save.


### Cloning Objects

When you call `clone` object is fully duplicated in memory into an independent record.

    $book_archive = clone $book;
    
    $book_archive->addCondition('is_archive',true);
    $book->addCondition('is_archive',false);
    
    $this->add('Grid')->setModel('book');
    $this->add('Grid')->setModel('book_archive');
    
This code will display two grids - one for regular books and another for archived. Because objects are cloned, adding condition on one will not affect another.

If you are to use cloning, you must keep one thing in mind:

**Hooks are not cloned**

Really - if you have a hook inside `Model_Book` to change the value before saving:

    function init(){
        parent::init();
        
        $this->addField('title');
        $this->hasOne('Author');
        
        $this->addHook('beforeSave',array($this,'my validate'));
    }

    function myvalidate($m) {
        if (strlen($this['title'])<10) {
            throw $this->exception('Title is too short');
        }
    } 
       
Can you tell what will happen if you clone the object with a hook like this? `$this` will be referencing an incorrect object! `$book_archive->save()` will call `$book->myvalidate()` and `$this` will validate the value of the `$book` instead of `$book_archive`.

You can avoid this problem if you use `$m` instead of `$this` inside a hook. In the above example `$m` will point to `$book_archive`.

## Object Naming

When objects are added, `add()` assigns them the unique name. Agile Toolkit make sure that within the same Application, no objects have the same `name`. It makes this an ideal property to use inside `<div id="..">`, GET arguments or session.

Typically Agile Toolkit will append name of the class to the `$owner->name` along with a number, but if you specify second argument to the `add()` you can slightly alter the name.
    
    // Automatic naming
    $my_object = $api->add('myClass');

    // The name property is unique to the application
    // and is based on the realm and class name
    $name = $my_object->name;

    // The short_name property is unique to the object within parent
    $short_name = $my_object->short_name;

    // Manual naming (used typically for fields)
    $my_object = $owner->add('myClass', 'foo');
    echo $my_object->name;          // realm_name_of_owner_foo
    echo $my_object->short_name;    // foo

## Element Tracking 

If you were carefully reading this chapter, in section `Accessing Added Objects` I have said the following: Views automatically can be accessed through `$owner->getElement`, but to aid garbage collection, Models can't be accessed. If you call getElement() and look for model, you'll get `true` instead of an object.

If you really want to keep reference to the Model, you should use setModel() or track it yourself. 

Actually - there is a class property, which affects this behavior: `auto_track_element` and when object is initialized if it's true then element array will contain reference to your object.

    $this->add('Model_Book',array('auto_track_element'=>true, 'name'=>'mybook'));

The above code will override default behavior and you can access the model by `$this->getElement('mybook')`. I must stay that this is a very unlikely use-case.


## Object Properties

As we have seen, `AbstractObject` provides a number of useful properties to every object in your app. Here's a complete reference:

<table>
    <thead>
        <tr>
            <th>Property</th>
            <th>Access*</th>
            <th>Description</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>short_name</td>
            <td>Read</td>
            <td>Object name unique to its parent's 'element' array.</td>
        </tr>
        <tr>
            <td>name</td>
            <td>Read</td>
            <td>Object name unique to the entire application.</td>
        </tr>
        <tr>
            <td>elements</td>
            <td>None</td>
            <td>Array containing references to child objects for element tracking. Where tracking are not required, objects may be 'detached' and their `elements` value will be `true`. This helps conserve memory.</td>
        </tr>
        <tr>
            <td>owner</td>
            <td>Read</td>
            <td>Points to the object which created this object through the call to `add()`</td>
        </tr>
        <tr>
            <td>api</td>
            <td>Read</td>
            <td>Always points to the application object, the topmost object in the system</td>
        </tr>
        <tr>
            <td>model</td>
            <td>Read</td>
            <td>Points to Model objects set with `setModel()`</td>
        </tr>
        <tr> 
            <td>controller</td>
            <td>Read</td>
            <td>Points to Controller objects set with `setController()`</td>
        </tr>
        <tr>
            <td>auto_track_element</td>
            <td>Default</td>
            <td>Regulates whether adding this object will automatically add a reference to the owner's `elements` array. If set to `false`, the object will be 'detached'</td>
        </tr>
    </tbody>
</table>

--* It's recommended that you do not change any of the properties. They all are declared as `public`, however that's primarily for read access and to make it possible for some system controllers to access those.

Below is a list of methods, which will affect some of the properties:

* $short_name, $name: `$object->rename('new_name')`
* $elements: `$object->add(); $object->getElement(); $child->destroy()`
* $owner: `$new_owner->add($object)`
* $api: none
* $model: `$object->setModel()`
* $controller: `$object->setController()`
* $auto_track_element: `add(.., array('auto_track_element'=>true));`

## See Also
There are more features in a typical AbstractObject which are described in other chapters:

* Hooks
* Dynamic Methods
* Exceptions
* Debug
* Session mgmt
* each()