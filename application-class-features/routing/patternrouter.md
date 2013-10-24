# SEF Link routing with PatternRouter 

Agile Toolkit comes with a simple two-way routing controller called Controller_PatternRouter.

    $api->add('Controller_PatternRouter')
        ->link('user/edit/:id')
        ->link('browse/:category/:action', 'browse/:action')
        ->route();
        
Pattern Router works according to a simple principle of dissembling "page" into GET parameters and then reassembling. When URL are generated, it simply reverses the arguments to reconstruct the original URL.

If the above example is accessed using URL: `browse/furniture/share`, the numeric arguments will be moved to $_GET['category'] and $_GET['action'] respectively. Without second argument request would be routed to the `browse` page, however in it's presence the URL is reconstructed back to `browse/share` and the category is passed as a get argument.

Note, that if for some reason you'll receive request like this:

    browse/share?category=furniture
    
then it will go through without re-routing and will access the same page. 

When you use `url()` the reverse process applies and the URL is matched against second arguments (or if it is omitted then against first argument with any dynamic sections stripped out). 

    url('browse/details',array('category'=>'fashion'));
    
Would produce URL:

    browse/fashion/details

Transparency is the most important feature of pattern routing in Agile Toolkit - as soon as you add a new route, application will begin using it through URL.

## Consuming multiple parts of the page

A regular route breaks down your request into parts, such as foo/bar/baz. Regardless of what variables you use, you must supply 3 parts separated with slashes. Sometimes you would want to match variable amount of sub-pages:

    ->link('doc/::rest')
    
In this case all sub-pages of doc/ will be matched and remainder of the page will be placed inside the `::rest`, e.g.: `doc/application/routing/url`

## Regular Expressions

You can also match certain arguments against regular expressions. The format is the following:

    ->link('browse/:id~[0-9]/:action~[a-z]')

This will instruct pattern router to patch id against numeric patterns and action against alphabetic sequences. Regular expression must always fully match (from ^ to $).

You can't match part of the section against a variable otherwise that would break the reversing ability.

## Types

For convenience you can use a simplified form on some of the regular expressions:

    ->link('browse/:id=int/:action=az')
    
Which is equal to the same pattern as above.

## calling route() and url()

If you call route() without any arguments, then PatternRouter will re-write `$api->page` and `$_GET`. You can however specify two arguments - page and arguments. If you do that, then route() will return new page and will modify the array to contain new argument set. 

Method `url()` performs the same but other way around.

## Conditional Routing

When calling `link()`, the third argument can be specified as routing condition callback. This function will be executed and only if it returns "true" in additional to regular matching, the substitution will take place. This allows you to use really interesting approaches, where you would route request to one of the several pages depending on conditions.



### Compatible formats

Before we arrived at the above routing, several other formats were used with PatternRouter. Those are obsolete and should be avoided:

    ->link('user/edit',array('id'))  // OBSOLETE
    
This line would be identical to route user/edit/:id. This format have several disadvantages, e.g. it could not pick arguments from within the URL.

## Tutorial: Beautiful blog URL matching

Would you want to match article URLs without impact on the rest of the pages?

    http://example.org/503-my-article-title

This tutorial will also explain to you how to generate URLs containing combined values.

## Advanced Tutorial: Building your own router

Find out how you could develop your own two-way routing controller working entirely based on your desired logic.