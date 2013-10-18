# INTRODUCTION TO TEMPLATES
----

Agile Toolkit uses it's own lightweight template engine. Terminology defines the following terms:

Opening Tag — alphanumeric sequence of characters surrounded by `<?` and `?>` (example `<?elephant?>`)

Closing tag — very similar to opening tag but surrounded by `<?/` and `?>`. If name of the tag is omitted, then it closes matching tag. (example `<?/elephant?>` or `<?/?>`)

Empty tag — consists of tag immediately followed by closing tag (such as `<?elephant?><?/?>`)

Self-closing tag — another way to define empty tag. It works in exactly same way as empty tag. (`<?$elephant?>`)

Region — typically a multiple lines HTML and text between opening and closing tag. (`<?elephant?>`Hello, World`<?/?>`)

Nested tag — tag located inside a region `<?elephant?>`Hello, `<?name?>`World`<?/?><?/?>`

## SMlite — template manager
This is a class defined in lib/SMlite.php which will read template from file into memory and will allow developer to interact with template. Templates in Agile Toolkit contains no logic, however certain views may introduce their own logic. Let's go over the basic functionality of SMlite first to see what can we do with the following template:

    <?greetings?>Hello, <?name?>World<?/?><?/?>
### Initializing SMlite
Like any other object in Agile Toolkit, you can use the following form:

    $template = $this->add('SMlite');
### Loading template
Template can be loaded from either file or string by using one of following commands

    $template->loadTemplateFromString($str);
     // OR
    $template->loadTemplate('mytemplate');
SMlite will use PathFinder to locate template in one of the directories (see previous chapter for default directory list). By default .html extension will be appended to the name of the template file.

### Rendering template
Ultimately we want to convert template into something useful. Rendering will return contents of the template without tags.

    $result=$template->render();

    $this->add('Text')->set($result);
    // Will output "Hello, World"
***Why not "echo" the result?***

*Agile Toolkit discourages direct output. You may try using "echo" for debug purposes, but never leave it in production environment. You should use objects instead, such as "Text". Text will automatically escape output for browser output too.*

### Setting value
After template is loaded, you can change contents of any region:

    $template->set('name','Peter');

    // Will contain "Hello, Peter"
You can also specify hash to `set()` with tag/new value.

### Getting value
Often templates are used to get values too. Let's put name of the person we are greeting into uppercase

$   template->set('name', strtoupper($template->get('name')));

    // Will contain "Hello, WORLD"

### Template cloning
When you have nested tags, you might want to extract some part of your template and render it separately. For example, you may have 2 tags SenderAddress and ReceiverAddress each containing nested tags such as "name", "city", "zip". You can't use set('name') because it will affect both names for sender and receiver. Therefore you need to use cloning.


    <div class="sender">
    <?Sender?>
    <?$name?>
    <?$street?>
    <?$city?> <?$zip?>
    <?/Sender?>
    </div>

    <div class="recipient">
    <?Recipient?>
    <?$name?>
    <?$street?>
    <?$city?> <?$zip?>
    <?/Recipient?>
    </div>

        
    $template=$this->add('SMlite');
    $template->loadData('envelope');        // templates/default/envelope.html

    // Split into multiple objects for processing
    $sender=$template->cloneRegion('Sender');
    $recipient=$template->cloneRegion('Recipient');

    // Set data to each sub-template separately
    $sender->set($sender_data);
    $recipient->set($recipient_data);

    // render sub-templates, insert into master template
    $template->set('Sender',$sender->render());
    $template->set('Recipient',$recipient->render());

    // get final result
    $result=$template->render();

### More operations
You can also `del('name')` to empty contents of the region, or `append('name',' and Steve')`. You can also call `is_set('name')` to find out if such tag exists. Finally methods `trySet` and `tryDel` can be used if you are not entirely sure if such tag will exist and would rather have your code do nothing if tag is missing rather than raise exception.

### Operations on multiple tags
Same tag can be used multiple times. If you will "set" such a tag, then all regions will be changed. `get()` will return contents of a first region. `append()` will add content to each region.

You can also use `eachTag()` to iterate through those tags.

`<?mywiki?>Continue to <?page?>about<?/?> page or <?page?>history<?/?> page<?/?>`
    
    $tempalte->eachTag('page',function($val){
        return '<a href="'.$val.'.html">'.ucwords($val).'</a>';
    });

    // Will contain "Hello, WORLD"

If your callback function defines second argument, then it will receive "unique" tag name which can be used to access template directly. This makes sense if you want to add object into that region. You can't insert object into SMlite template, however every view in the system will have it's template pre-initialized for you.

## Views and Templates
Now that you understand how raw templates work, let's see how views use them.

### Default template for a view
By default view object will execute `defaultTemplate()` method which returns location of the template. This function must return array with one or two elements. First element is the name of the template which will be passed to `loadTemplate()`. Second argument is optional and is name of the region, which will be cloned. This allows you to have multiple views load data from same template but use different region.

Function can also return a string, in which case view will attempt to clone region with such a name from parent's template. This can be used by your "menu" implementation, which will clone parent's template's tag instead to hook into some specific template

    function defaultTemplate(){
        return array('greeting');   // uses templates/default/greeting.html
    }

### Redefining template for view during adding
When you are adding new object, you can specify a different template to use. This is passed as 4th argument to `add()` method and has the same format as return value of `defaultTemplate()` method. Using this approach you can use existing objects with your own templates. This allows you to change the look and feel of certain object for only one or some pages. If you frequently use view with a different template, it might be better to define a new View class and re-define `defaultTemplate()` method instead.

    $this->add('MyObject',null,null,array('greeting'));

### Accessing view's template
Template is available by the time `init()` is called and you can access it from inside the object or from outside through "template" property.

    $grid=$this->add('Grid',null,null,array('grid_with_hint'));
    $grid->template->trySet('my_hint','Changing value of a grid hint here!');

In this example we have instructed to use a different template for grid, which would contain a new tag "my_hint" somewhere. If you try to change existing tags, their output can be overwritten during rendering of the view.

### How views render themselves
Agile Toolkit perform object initialization first. When all the objects are initialized global rendering takes place. Each object's `render()` method is executed in order. The job of each view is to create output based on it's template and then insert it into the region of owner's template. It's actually quite similar to our Sender/Recipient example above. Views, however, perform that automatically.

In order to know "where" in parent's template output should be placed, the 3rd argument to `add()` exists — "spot". By default spot is "Content", however changing that will result in output being placed elsewhere. Let's see how our previous example with addresses can be implemented using generic views.

    $envelope=$this->add('View',null,null,array('envelope'));

    // 3rd argument is output region, 4th is template location
    $sender=$envelope->add('View',null,'Sender','Sender');
    $receiver=$envelope->add('View',null,'Receiver','Receiver');

    $sender->template->trySet($sender_data);
    $receiver->template->trySet($receiver_data);

## Best Practices
### Don't use SMlite directly
It is strongly advised not to use templates directly unless you have no other choice. Views implement consistent and flexible layer on top of SMlite as well as integrate with many other components of Agile Toolkit. The only cases when direct use of SMlite is suggested is if you are not working with HTML or the output will not be rendered in a regular way (such as RSS feed generation or TMail)

### Organize templates into directories
Typically templates directory will have subdirectories: "page", "view", "form" etc. Your custom template for one of the pages should be inside "page" directory, such as page/contact.html. If you are willing to have a generic layout which you will use by multiple pages, then instead of putting it into "page" directory, call it "page_two_columns.html".

You can find similar structure inside atk4/templates/shared or in some other projects developed using Agile Toolkit.

### Naming of tags
Tags use two type of naming - CamelCase and underscore_lowercase. Tags are case sensitive. The larger regions which are typically used for cloning or by adding new objects into it are named with CamelCase. Examples would be: "Menu", "Content" and "Recipient". The lowercase and underscore is used for short variables which would be inserted into template directly such as "name" or "zip".

### Don't Repeat Yourself (DRY)
You must always remember and your designer must also know the DRY principle. Avoid having exactly same piece of code on all the pages. If you must place "disclaimer" on multiple pages, you can use this simple syntax:

    $page->add('View',null,'Disclaimer',array('disclaimer'));

### Take advantage of global setTags
Application (API) has a function `setTags($t)` which is called for every view in the system. It's used to resolve "template" and "page" tags, however you can add more interesting things here. For example if you miss ability to include other templates from Smarty, you can implement custom handling for `<?include?>` tag here.

Be considered that there are a lot of objects in Agile Toolkit and do not put any slow code in this function.