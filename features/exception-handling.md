# USING EXCEPTIONS IN AGILE TOOLKIT

Agile Toolkit provides a wrapper for creating exceptions. The normal syntax:

    throw new exception('bleh');

is NOT used in Agile Toolkit, instead the following synax is used:

    throw $this->exception('bleh');

There are several advantages why this syntax is used and they are possible due to dynamic nature of PHP language.

### Linking exception with a class
using "new" creates a class, but it's not associated in any way with a current object. When exception is captured it will be impossible to figure out where it originated. Calling `exception()` will automatically associate exception object with "$this" class.

### Object-specific Exception class selection
Exception raised in model is different to exception raised in PathFinder or DSQL. Each object of Agile Toolkit can define default exception class which will be initialized by `exception()` method. When throwing exception, developer should not worry about which class should be used.

### Chaining methods before throwing
PHP does not allow to chain object which was created with "new". Standard syntax will require a temporary variable and multiple statements. The Agile Toolkit syntax is a single statement and requires no variables:

    if($temperature>70)
      throw $this->exception('Temperature too high')
        ->addMoreInfo('temperature',$temperature);

### Localization of error messages
The error message specified to exception() must not contain any variables. This message will be routed through `$api->_()` method for localization.

### Verbosity control
Sometimes you may want to display error message to the user. Certainly you would want to hide any additional information as it may have some sensitive data.

### Object to add more info automatically
Your class may extend `exception()` method to automatically add more information from the object properties before throwing such as `Model_Table::exception()`.