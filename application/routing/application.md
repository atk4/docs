# Application - dependent routing
So far we have only looked into ways how "page" is being mapped into a class. This all happens during the inclusion of `PageManager` controller or when you are using `PatternRouter` controller.

Next, the main application class gets to decide what to do with this information. Let's look at existing application classes and what do they do:

API | Generated URL 
--- | --- | ---
ApiCLI | Does not include PathFinder or perform any routing at all
ApiWeb | Initializes PageManager, however does not take advantage of pages. This still allow views to generate URLs within the same page. Use this class when included into other framework or when your application needs no pages.
ApiFrontend | Initializes PageManager then locates 'Page' class, static template or a function.
ApiAdmin | Retains functionality of ApiFrontend, but also adds several automated routes for advanced content management.
ApiInstall | Based on ApiWeb, but adopts page names into "steps" for building a single-file installers.

As you introduce your own application class (and you most probably do that anyway in `lib/Frontend.php`) you can define additional rules for routing. Agile Toolkit must remain creative and think of the most efficient way to structure your own application.

## ApiCLI
Although I said that this does not use routing, you may still need to generate URLs. For instance, if you are sending out automated emails and use template.

Class `URL` requires PageManager to be initialized, but ApiCLI does not initialize it. You can still add this:

    $api->add('Controller_PageManager')->setURL('http://mysite.com/admin/');
    
Specified URL will be split into two parts: `base_url` and `base_path`. The URL assumes that the given page is empty (index)

    echo $api->url('test'); 
    
will produce `http://mysite.com/admin/test` or `http://mysite.com/admin/?page=test` depending on your config.

## ApiWeb
ApiWeb does not have any routing schemes, therefore by default URL will be linked back to the same PHP file. For intense if you have file `test.php` like this:

    include 'atk4/loader.php';
    $api = new ApiWeb();
    echo $api->url('.')->absolute();

then it will output full path to the `test.php` file - same thing as in your URL.

If you need to integrate with other framework routing mechanism, you might implement `api::url()` method to the requirement of your framework.

## ApiInstall
ApiInstall will keep you on the same page as you go through the steps, but it will adjust a sticky `?step=` argument.

When initialized, it will look for method `step_<step>`

## ApiFrontend
This application class introduces a concept of Layout. It's generally a portion of the page which may be present. Here are example layout chunks:

 * Content - area for the main content of your page
 * Menu - area for menu. Would not be there when printing
 * UserMenu - Area occupied by a user's icon and his profile menu. Would not be there if user is not logged in
 * Breadcrumb - Shows user's location. Might not be there when printing article.
 
User may use the above or add additional layout sections. For any section to work, two things must be done:

 - `addLayout('Breadcrumb');` must be called in application's init
 - `<?$Breadcrumb?>` must be present in your application template 