# Core > Dynamic Methods

## What Are Dynamic Methods?

In PHP if method of the object is not defined, then `$method->__call()`
is called instead. Agile Toolkit objects place hook inside this function
with a corresponding hook name. To make this feature simpler to use,
there are few methods to "register" new methods inside object.

    $obj->addMethod('test', function($obj, $arg1, $arg2){
        echo "Values are $arg1, $arg2";
    });

    $obj->test(1,2);  // Output: Values are 1, 2

Similarly like whith any hooks, the first argument to your call-back
function is the object itself.

With the ability to define custom methods a standard `has_method()` is
not insufficient, therefore Agile Toolkit provides you with another
method to check if the method is defined or not:
`$object->hasMethod();`. This returns true if either a static or dynamic
methods exists.

Dynamic methods are generally registered by some controllers and due to
more complicated code structure should be avoided.

(this is similar to javascript code obj.test=function(){ .. });)

## Global dynamic methods

If method is not defined inside object, then a global table of methods
is checked inside the $api. You can also register a global method, which
will then be present in all the objects created under the same
application. Note that `addGlobalMethod` is the method of Application
class.

    $obj->api->addGlobalMethod('test', function($obj,$arg1,$arg2){
        echo "Values are $arg1, $arg2";
    });

    $this->add('OtherObject')->test(1,2);

(this is similar to javascript code obj.prototype.test=function(){ .. };)

# Practical Example

i--> link to article building activity logger for generic model.

# Application Hooks

These enable you to run code at specific points in the execution flow:


* ApiCLI
  * caught-exception(api,exception) - called when your application
generates exception. Logger contoller is using this hook to intercept
and highlight exceptions better.
  * output-warning, output-debug ($msg) can be used to nicely output
errors or log them. Also intercepted by Logger.
  * localizeString($str). Hook can be used to register localization
mechanism. All the texts of Agile Toolkit (such as labels) will be
passed through this hook which must return localized version. See also
internationalization.
* ApiWeb
  * pre-init, pre-exec, post-submit, pre-render, post-js-collection,
pre-render-output and post-render-output - hooks are always executed from the
main() method. You can divert some element initalization to the hook if
you want to make sure that everything else is initialized. For example
if you want to output how many database queries were Performed during
applicaiton run, you need to output it on 'post-render-output'
  * cut-output, submitted - thoes hooks are called in certain
conditions. cut-output is called when only part of the page is being
redrawn. submitted is called when POST data is received from any form.
(Form object actually uses this hook to process load post data)

* Auth/Basic
  * login - hook is called when user logs in 
    TODO: List of available hooks, please!

[[[[[[[[[[[[[[[[ WORKING HERE ]]]]]]]]]]]]]]]]]
