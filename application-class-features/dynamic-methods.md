# Core > Dynamic Methods

## What Are Dynamic Methods?

With all objects in Agile Toolkit, you can add your own methods at runtime.

Under the hood, `AbstractObject` uses hooks and PHP's [__call() Magic Method](http://www.php.net/manual/en/language.oop5.overloading.php#object.call) to provide this functionality.

## How To Add A Dynamic Method

The syntax for dynamic methods is:

     addMethod($name, $callable)

where `$callable` is a closure. As with hooks, the first argument passed into your callback handler will be a pointer to the object that contains the method. Any additional arguments you pass when calling your dynamic method are added in order, starting with the second argument:

    $obj->addMethod('test', function($obj, $first_arg, $second_arg){

        echo "Values are $first_arg, $second_art";
    });

    $obj->test(1, 2);  // Outputs: "Values are 1, 2"

## How To Test For A Dynamic Method

If you need to test for the presence of a dynamic method in an object, the standard `has_method()` is no longer sufficient. So Agile Toolkit provides `$obj->hasMethod()`, which returns true if either a static or dynamic methods exists.

## Global dynamic methods

You can also register a global method which will be present in all the objects created under the same application. 

Note that `addGlobalMethod()` is the method of the Application `api` class:

    $obj->api->addGlobalMethod('test', function($obj, $first_arg, $second_arg){

        echo "Values are $first_arg, $second_arg";
    });

    $this->add('OtherObject')->test(1, 2); // Outputs: "Values are 1, 2"
