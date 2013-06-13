# Core > Hooks and Dynamic Methods
<!-- Romans reviewed 10/04 -->

## What Are Hooks?

!!! NOTE: Let's call 1st argument - "Spot". In PHP it's called $hook_spot.

Hook is a well know development pattern which allows to enhance
functionality of the application by registering callbacks.

http://en.wikipedia.org/wiki/Hooking

Agile Toolkit core implements ability to register and execute hooks. The
functionality is very similar to methods "bind" and "trigger" in jQuery.
Other frameworks may use similar techniques and call them "actions",
"handlers" or "callbacks". 

Here is a very typical examples, how you would use hooks in Agile Toolkit:

    $model->addHook('beforeSave', function($m){
        $m['search_text']=strip_tags($m['text']);
    });

By calling addHook the specified callable is associated with the list of
callbacks under the 'beforeSave' key. Before model is being saved, it executes:

    $this->hook('beforeSave');

and it causes all registered methods to be executed in the same order as
they were registered.

## Objects and Hooks

Software like wordpress is using global array storing hooks in order to
allow add-ons extend it's functionality. Hooks have been used in other
applications also. Desktop environments are often using call-backs, but
those callbacks are object-level. That means if you have 2 objects you
can register callback for only one objects. Some frameworks also
implement a similar concepts of "slots" but essentially it's the same
thing.

In Agile Toolkit any object has addHook() method and you can register
your callback using any hook identifier.

    $someObj('mytest', $callable);

In addition to specifying callables, you can also specify a regular
object as a second argument, in which case hook will call same method as
the name of the hook.

    $someObj('mytest', $otherobj); // Same effect as:
    $someObj('mytest', array($otherobj, 'mytest'));

If you can register same handler with multiple objects. Your handler's
first argument always is pointing to the object which owns the hook.


    $model1->addHook('test',$me);
    $model1->api->addHook('test',me);

    $model->hook('test');  // executes $me->test($model1);
    $otherobject->api->hook('test'); // $me->test($api);

## Hook arguments

Both the hook() method and addHook() can contribute some arguments
towards the handler. Normally call-back is executed only with a single
argument caller object.

    $obj->addHook('test',function($m,$arg1,$arg2){
        echo "arguments are $arg1, $arg2";
    }, array(2));
    $obj->hook('test',array(1));

    // Output: arguments are 1, 2

## Hook priorities

Normally callbacks are executed in the same order as you assign, but you
can specify higher or lower priority on certain callbacks.

    $obj->addHook('test',function(){ echo "def "; });  // priority 5
    $obj->addHook('test',function(){ echo "2 "; },null, 2);
    $obj->addHook('test',function(){ echo "10 "; },null, 10);

    $obj->hook('test');

    // Output: 2 def 10

If you specify negative priority then hooks are executed in a reveres order

   
    $obj->addHook('test',function(){ echo "def1 "; }); 
    $obj->addHook('test',function(){ echo "def2 "; });
    $obj->addHook('test',function(){ echo "rev1 "; },null, -3);
    $obj->addHook('test',function(){ echo "rev2 "; },null, -3);

    $obj->hook('test');

    // Output: rev2 rev1 def1 def2

## Returning hook values

Method `hook` will return array containing results of all the hooks. If
at least one hook executed, it will be non-empty array, therefore you
can also use this syntax to detect if there were any hooks:

    if(!$this->hook('test')){
        echo "no hooks!";
    }

Here is an example where we call multiple hooks and use combined value

    $obj->addHook('foo',function($o){ return 1; });
    $obj->addHook('foo',function($o){ return 2; });
    $res = $obj->hook('foo'); // returns array(1,2);

## Breaking The Chain Of Execution

It's possible for a callback to stop execution of further call-backs.
When called from inside a hook callable, `breakHook()` will break the
chain of execution, and the `hook()` method will return the value
passed:

    $obj = $this->add('MyClass');

    $obj->addHook('foo',function($o){ return 1; });
    $obj->addHook('foo',function($o){ 

            $o->breakHook('override value'); 
        });
    $obj->addHook('foo',function($o){ return 1; });

    $res = $obj->hook('foo'); // returns 'override value';

## Removing All Hooks From A Spot

It is possible to remove hook from a spot  To remove all hooks from a spot:

    $obj = $this->add('MyClass');

    $obj->addHook('foo', function($o){ return 1; });
    $obj->addHook('foo', function($o){ return 2; });

    $obj->removeHook('foo');
    $res = $obj->hook('foo'); // array();


## Dynamic Methods

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








# Model Hooks

    TODO: List of available hooks, please!

# Other object hooks

    TODO: List of available hooks, please!
