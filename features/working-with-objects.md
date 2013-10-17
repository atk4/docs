# Working with Objects
# CREATING OBJECTS AND RUNTIME TREE

All objects in Agile Toolkit applications are arranged into a tree. Any object is capable of having child objects and the top-most (root) node is the Application object.

| PROPERTY           | DESCRIPTION |
|--------------------|-------------|
| short_name         | System object name which identifies objects through it's parent's "element" array. If you have to change the name, use `rename()`.|
| name               | Unique object name. No two objects in the system will have the same name. |
| elements           | Array containing references to all child objects. Since 4.2, some objects may be "detached" in which case instead of a reference, the elements array will contain "true" value. This helps conserve memory. |
| owner              | Always pointing to object which created this object through the call to `add()` |
| api                | Always points to application object, the top-most object (root) |
| model              | Points to "model" object, set with `setModel()` |
| controller         | Points to "controller" object, set with `setController()` |
| auto_track_element	 | Regulates if adding this object will automatically include link in owner's elements[] array. If set to false, object will be "detached" |

## Detached Objects
Since 4.2 the concept of "Detached Objects" has been introduced. Pretty often you would have a code like this in your application.

    $m=$this->add('Model_User');
    $m->load(4)->set('name','John')->save();

When this code ends the execution and scope of $m is no longer available, then $m should be disposed off. PHP does that automatically through garbage collection, if $m has no more references. Prior to 4.1 add() would also create reference to the `$m` through `$this->owner->elements['Model_User']` and even if $m is no longer available, the model object would still reside in memory

Creating a loop which initialized series of objects and then disposing needs to properly dispose of objects.

Detached Objects is the solution to this problem. Models and Controllers are "detached" by default, which means they will create a record in $owner->elements[] but it will not point back to the object.

Method `add()` will check "auto_track_element" property and will only create reference in `$elements` only if "auto_track_element=true".

### Typical objects which are NOT Detached
Objects which will keep link to themselves are:

* All the Views
* Model Fields
* Model Joins and hasMany relations

## Preconfiguring objects
The typical flow of Agile Toolkit objects would create them and then configure them through a follow-up calls. The rule here is that the `init()` method must perform absolute minimum initialization and the rest of the work should either done by other methods such as `setModel()` or `render()`.

In certain cases, Agile Toolkit would initialize all the page objects, but would render only some objects, therefore to keep performance of your application, attempt to do all the hard work in `render()`.

There is however a way to pass in data into the `init()` method. If you have worked with frameworks such as Yii or Sencha there is a way to pass. If you need to pass configuration and the name at the same time, use 'name' key.

    class MyObject extends AbstractModel {
        function init(){
            parent::init();

            echo $this->di_config['foo'];
        }
    }

    $this->add('MyObject',array('foo'=>'bar'));   // outputs "bar";

>Even through there is a way to pass data into `init()`, try avoid using this approach.

## Object Manipulation
###GENERATED OBJECT NAME
When you call `add()`, the second argument defines the "short_name" of the object. If it's omitted or "null", then the class of the object is used. If there is already object with such a short-name, then call `to _unique()` alters the short_name to make it unique.

    $b1=$this->add('Button');
    $b2=$this->add('Button');

    echo $this->name."<br/>";   // outputs realm_page_name
    echo $b1->name."<br/>";     // outputs realm_page_name_Button
    echo $b2->name."<br/>";     // outputs realm_page_name_Button_2

### RETRIEVING OBJECTS
`getElement()` will return of a sub-element by it's short-name. Even through "elements" is public, use `getElement()` instead. If element is not defined, this will throw exception.

`hasElement($name)` works same as `getElement()` but will return "false", "true" or the objects itself and will not produce exception. It's safe to use `hasElement` as a `if()` condition.

Once object is added, you can rename it by callign `$object->rename();` Please try to avoid using `rename()` as the object may have already have some dependencies using the `short_name` and it might break some functionality.

### RE-ADDING OBJECTS
Once object is added, you can add it into a different parent by calling `$newparent->add($object);`

### DESTROYING OBJECTS
If you have added object and then you need to remove it, call `$object->destroy();` A typical scenario if you need to remove field in a model from the model it extends.

    class Model_Admin extends Model_User {
        function init(){
            parent::init();

            $this->getElement('email')->destroy();
        }
    }

You can also use `removeElement('name')`, which is safe way to deal with detached objects

### NAMESPACES
Agile Toolkit uses namespaces for Add-on management. Here is how you define a class inside a add-on and call it:

    // inside atk4-addons/myaddon/Test.php
    namespace myaddon;
    class Test extends \View {    // backslash is not typo. It refers to a top-level namespace
        function init(){
            parent::init();
            $this->set('Hello from Add-on');
        }
    }

    // Using class from add-on
    $this->add('myaddon/Test');

To learn more about developing add-ons, please read [Add-on developer guide](developing-addons.md "Add-on developer guide").