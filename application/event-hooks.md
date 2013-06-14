# Core > Hooks

## What Are Hooks?

Hooks are [a well know development pattern](http://en.wikipedia.org/wiki/Hooking) which enables you to register callbacks with an object. 

This is very similar to the "bind" and "trigger" methods in jQuery. In other frameworks they may be called "actions", "handlers" or "callbacks". 

In Agile Toolkit, hooks are implemented in `AbstractObject` so all classes have access to hook functionality.

If you're not familiar with this pattern, here's the basic idea...

First, an object offers a "spot" where you can register callback code. So in Model_Table, for example, you'll find:

     function save(){

        ...

        $this->hook('beforeSave');
    }

Then in your Form you might have:    

    $model->addHook('beforeSave', function($m){

        $m['card_mask'] = substr($m['card_num'], -4);
    });

Now when your Model is saved the hook will execute and the code in your callback will be run.

You could also add a `beforeSave` hook inside a Model that extends Model_Table:

    class Model_Customer extends Model_Table {
        
        function init(){
          
            ...
            
            $this->addHook('beforeSave',function($m){

                $m['updated_at'] = date('Y-m-d G:i:s', time());
            });
        }
    }

You can add more than one hook to the same spot, and by default they run in the order they were registered.

Hooks offer a convenient way to extend an object's functionality at runtime. Many objects in the Toolkit offer hooks, and you can use them to add flexibility to your own objects as well.

## How To Create A Hook Spot

As we've seen, you simply call `hook()` at the spot in your object where you want the callback handlers to run. 

    $this->hook('beforeSave');

## How To Add Hooks To A Spot

Here are the arguments for `addHook()`;

    addHook($hook_spot, $callable, $arguments = array(), $priority = 5)

The first argument is the name of the spot you are hooking into, and the second is a callable PHP type that will be called when the hook runs. 

As we've seen, the typical callable is a closure:

    $this->addHook('beforeModify', function($m){

        ...
    });

You can also pass in an array, with the callback handler object as the first value, and the name of the method to be called on the handler as the second value.

    function addRequest()
    {
        ...

        $this->addHook('request-complete', array($this, 'requestComplete'));
    }

    function requestComplete($request, $response)
    {
        ....
    }

Finally, if you pass in an object rather than an array, the hook will call a method on the handler with the same name as the hook spot:

     $this->addHook('request-complete', $this);

     // ...is shorthand for:

     $this->addHook('request-complete', array($this, 'requestComplete'));

## Hook Arguments

Both `hook()` and `addHook()` can pass arguments to the callback handler.

The object containing the hook spot always passes itself into your callback handler as the first argument:

    $my_obj->addHook('beforeModify', function($my_obj){

        ...
    });

The `hook()` method can pass values to the callback handler as an array in its second argument:

    $val = 'test-1';
    $this->hook('test', array($val, 'test-2'));

The arguments set in the array will be passed to the callback handler in order, starting with the second argument:

    $obj->addHook('test', function($obj, $first_arg, $second_arg){

        // Outputs "test-1 :: test-2"
        echo("$first_arg :: $secong_arg");
    });

The `addHook()` method can pass additional arguments as an array in its 3rd argument. These are passed to the callback handler after the arguments passed by `hook()`;

    $val = 'test-3';

    $obj->addHook('test', function($obj, $first_arg, $second_arg, $third_arg, $fourth_arg){

        // Outputs "test-1 :: test-2 :: test-3 :: test-4"
        echo("$first_arg :: $secong_arg :: $third_arg :: $fourth_arg");

    }, array($val, 'test-4'));
    
If you add the same handler to multiple objects, the first argument passed to the handler always points to the object that owns the hook.

    $model1->addHook('test', $obj);
    $model1->api->addHook('test', $obj);

    $model->hook('test');               // Executes $obj->test($model1);
    $otherobject->api->hook('test');    // Executes $obj->test($api);

## Hook priorities

By default callbacks are assigned a priority of 5 and are executed in the order they are assigned. You can change the order by passing a different priority as the 4th argument of `addHook()`. Lower numbers are executed first.

    // Outputs: "2 def 10"
    $obj->addHook('test',function(){ echo "def "; }); // priority 5
    $obj->addHook('test',function(){ echo "2 "; }, null, 2);
    $obj->addHook('test',function(){ echo "10 "; }, null, 10);

    $obj->hook('test');

If you specify a negative priority then hooks are executed in reverse order:
   
    // Outputs: rev2 rev1 def1 def2
    $obj->addHook('test',function(){ echo "def1 "; }); 
    $obj->addHook('test',function(){ echo "def2 "; });
    $obj->addHook('test',function(){ echo "rev1 "; }, null, -3);
    $obj->addHook('test',function(){ echo "rev2 "; }, null, -3);

    $obj->hook('test');

## Returning hook values

Method `hook()` will return an array containing the values returned by all the handlers added, listed in the order they were called. If at least one hook executed calling `hook()` will return a non-empty array, so you can also use this syntax to detect if there were any hooks:

    if( ! $this->hook('test')){

        echo "No hooks!";
    }

Here is an example where we call multiple hooks and use the returned values:

    $obj->addHook('foo', function($o){ return 1; });
    $obj->addHook('foo', function($o){ return 2; });

    $res = $obj->hook('foo'); // Returns array(1, 2);

## Breaking The Chain Of Execution

It's possible for a callback to stop execution of further call-backs.  When called from inside a hook callable, `breakHook()` will break the chain of execution, and the `hook()` method will return the value passed to `breakHook()`:

    $obj = $this->add('MyClass');

    $obj->addHook('foo',function($o){ return 1; });

    $obj->addHook('foo',function($o){ 

            $o->breakHook('override-value'); 
        });

    $obj->addHook('foo',function($o){ return 2; });

    $res = $obj->hook('foo'); // returns 'override-value';

## Removing All Hooks From A Spot

To remove all hooks from a spot:

    $obj = $this->add('MyClass');

    $obj->addHook('foo', function($o){ return 1; });
    $obj->addHook('foo', function($o){ return 2; });

    $obj->removeHook('foo');
    $res = $obj->hook('foo'); // returns array();

## Useful Hooks

Here are some of the most commonly used hooks, to give you an overview of what's on offer:

### Model Hooks

In many applications the most common use of hooks is in Models:

    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
    TODO: List of available hooks, please!
    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

### Application Hooks

ApiFrontend offers a number of hooks at different spots in the request response:

    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
    TODO: List of available hooks, please!
    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>

### Other Useful Hooks

    Here are some other hooks that are often useful:

    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
    TODO: List of available hooks, please!
    >>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
