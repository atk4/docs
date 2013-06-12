# Core > Exception Handling

## Using Exceptions In Agile Toolkit

Agile Toolkit provides a wrapper for creating exceptions. The normal PHP syntax:

    throw new exception('Oops!');

is NOT used in Agile Toolkit. Instead, use the `$this->exception()` method:

    throw $this->exception('Oops!');

There are several advantages to this approach.

### Linking an exception to a class

    // TODO: Example of how this is used, please

Using the `new` statement creates a class, but it's not associated in any way with a current object. When the exception is caught there's no indication where it originated. Calling `exception()` will automatically associate the exception object with the current `$this` class.

### Object-specific Exception class selection

An exception raised in Model is different to an exception raised in PathFinder or DSQL. 

Each object in Agile Toolkit can define a default exception class which will be initialized by the `exception()` method. When you throw an exception, the correct exception class will be used automatically.

    // TODO: can you provide an example here, please?

### Chaining methods before throwing exceptions

PHP does not allow the chaining of objects created with `new`. Standard syntax will require a temporary variable and multiple statements. The Agile Toolkit syntax is cleaner:

    if($temperature > 70)
    {
        throw $this->exception('Temperature too high')
        ->addMoreInfo('temperature', %temperature);
    }

### Verbosity control

    // TODO: yes, but how specifically does exception() help here? Example please.

Sometimes you may want to display an error message to the user, and you need to hide any sensitive data.

### Object to add more info automatically

Your class may extend the `exception()` method to automatically add more information from the object properties before throwing: 

    // In Model_Table

    function exception(){

        // TODO: Possible to give a clearer example?

        return call_user_func_array(array('parent',__FUNCTION__), func_get_args())
                 ->addThis($this);
    }
