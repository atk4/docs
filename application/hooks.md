# Core > Hooks

## What Are Hooks?

Hooks are [a well know development pattern](http://en.wikipedia.org/wiki/Hooking) which enables you to register callbacks with an object. 

This is very similar to the "bind" and "trigger" methods in jQuery. In other frameworks they may be called "actions", "handlers" or "callbacks". 

In Agile Toolkit, hooks are implemented in `AbstractObject` so all classes have access to hook functionality.

If you're not familiar with this pattern, here's the basic idea...

First, an object offers a "spot" where you can register callback code. So in `Model_Table`, for example, you'll find:

     function save(){
        $this->hook('beforeSave');

        ...

    }

Then in **your code** you might have:    

    $model->addHook('beforeSave', function($m){

        $m['card_mask'] = substr($m['card_num'], -4);
    });

Model's `save()` method will now automatically call your callback method. This technique allows you to add behaviours to the model without overriding methods. You can place hook anywhere in your code even inside Model's `init()` method:

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

It is also a good idea to introduce hooks in your own software because all objects of Agile Toolkit already have this functionality, you just nee to add a call to `$this->hook()` where you want the callback handlers to run. 

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

        $this->addHook('requestComplete', array($this, 'requestComplete'));
    }

    function requestComplete($request, $response)
    {
        ....
    }

Finally, if you pass in an object rather than an array, the hook will call a method on the handler with the same name as the hook spot:

     $this->addHook('requestComplete', $this);

     // ...is shorthand for:

     $this->addHook('requestComplete', array($this, 'requestComplete'));

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

 * beforeLoad($model, $query) - called before loading SQL query is executed. You have a chance to modify that query. This is called for both model->load() and for iterating through model with foreach($model). This hook is great for applying extra options to your SQL query.
 * afterLoad($model) - called after data have been loaded from SQL. You can now access $model->get() and the model will appear to be loaded. Called for both model->load() and iterating. This hook is great for performing data manipulation and normalization.
 * beforeSave($model) - called when $model->save() is called. This is called inside SQL transaction, so database changes you perform here will be rolled back if save would be unsuccessful. This hook is great for performing data modification before it's been saved. You can check $model->loaded() to see if a new record is being stored or updated
 * beforeInsert($model, $query) - called when inserting new data and after the insert query is being formed. That query is passed as 2nd argument. This hook is great for changing insert query options.
 * afterInsert($model,$id) - called after insertion method is performed successfully, but before model is re-loaded. You can break out of this hook and return a substitute model. Great for overriding how model is reloaded after insert.
 * beforeModify($model,$query) - called before update SQL query is executed. This hook is great for changing update query options.
 * afterModify($model) - called after SQL query is executed but before reloading has taken place. Note that if you access set() / get() here it will be reloaded by a subsequental reload.
 * afterSave($model) - called after model have been successfully reloaded. This is the last hook to be executed before SQL transaction is finished with commit. Please note taht beforeLoad / afterLoad will also be called during the reloading of a model. This hook is great for hiding some fields from a model after they are being saved such as wiping your password field.
 * beforeUnload($model)
 * afterUnload($model)
 * beforeDelete($model,$query) - you can access id through $model->id
 * afterDelete($model)
 * beforeDeleteAll($model)
 * afterDeleteAll($model)

Note: Some of those hooks will not supply $query if used in non-sql models.


### Application Hooks

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
  * login - hook is called when user logs in (first time authentication), can be used to populate some session data from database.
  * tryLogin - called after setModel, can be used to login using cookie data. You receive $model, $login, and $password parameters and if user is login, respond with user_id.
  * isPageAllowed - use breakHook(true / false) to override default behaviour of this method. Can be used to punch some public pages through CMS.
  * check - called when password authentication is checked. Last chance to use breakHook(user_id) to authenticate user and bypass login.
  * loggedIn - executed after successful authentication
  
<!-- TODO romans: clarify difference between login and loggedIn -->
  
  