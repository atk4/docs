# Generating URLs, Linking, StickyGET

Agile Toolkit consist of pages. One page may want to link to another application. Agile Toolkit provides a generic wrapper which allows you to construct URLs based on what page you want to link to and what arguments you wish to pass:

    $link = $this->api->url('user/edit', array('id'=>72));
    
In default installation, the following link will be used:

    /my/admin/index.php?page=user/edit&id=72
    
If you configure your Agile Toolkit installation to use mod_rewrite, the link will be:

    /my/admin/user/edit?id=72
    
Finally if you use PatternRouter, the link can also look like this:

    /my/admin/user/edit/72
    
As a developer you should not be concerned about how your URL look like, because they may be different depending on installation parameters as well as routing config.

While it seems that `url()` is giving back a URL, it actually gives back a special object, which will render into URL when converted toString(). This is done to prevent double-use of url() through accidents and also to make it possible to add more arguments to the URL after it's being created.

[todo] link to URL class reference

## Relative URL

Because some of the add-ons may rely on URL generation they require portability. For simpler refactoring you should rely on relative URLs. They will generated different URL depending on the current value of $api->page. Here is example:

**Assuming you are on the page = /admin/user/profile/edit:**

Page | Generated URL | Notes
--- | --- | ---
/ | /admin/ | Always leads to application Base Path
. | /admin/user/profile/edit | Refers to current page. Passing `null` as first argument has same effect.
.. | /admin/user/profile | Parent page
../share | /admin/user/profile/share | Adjacent page
./confirm | /admin/user/profile/edit/confirm | Sub-page
logout | /admin/logout | Relative Page
//yahoo.com | http://yahoo.com/ | Off-site page with same schema
https://yahoo.com/ | https://yahoo.com/ | Off-site page of specific schema

## Exercise

Place the below example in your application, and you can discover how URL building works for yourself:

    $form = $this->add('Form');
    $in   = $form->addField('line','page');
    $out  = $form->addfield('line','relative_url');

    $in->js(true)->univ()
        ->autoChange(200)->disableEnter();
    $in->js('change', $form->js()->submit());

    if($form->isSubmitted()){
        $out->js()->val($form->api->url($form->get('page')))
            ->execute();
    });
    
    