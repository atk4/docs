# Core > Routing

## What Is Routing?

Routing is the process by which Agile Toolkit determines which code to run in response to a request.

Requests are passed to the webserver from the browser in the form of a URL. Each url should define a unique resource for your app to return to the requester.

Traditionally URL consists of the page itself (user/details) and then arguments (?id=123). Some web software applications try to hide everything into session arguments, others try to bloat it with lengthy arguments. Typical application in Agile Toolkit straights the right balance by only including information necessary to reproduce the page on the screen and tries to pass only "ids". Form data almost always is sent through the POST data.

Agile Toolkit routing designed to be simple, yet extensible and I will introduce it throughout a series of concepts.

### Who is responsible for Routing

Routing happens inside your "Application" class. That means that depending on the application, the routing may change. ApiFrontend relies on the **page name** concept, while the Installation Application class looks out for **installation step** instead.

Application delegates some of it's job to PageManager and optionally PatternRouter classes.

If you are using ApiCLI or ApiWeb, then there are no routing is not performed automatically.

### Reading the URL

Parsing the URL is a job of PageManager class. Accessible at `$api->pm` it will divide your URLs into 3 key parts accessible through `api->pm->base_url`, `api->pm->base_path` and `api->page`.  

    http://yoursite.com/install/dir/user/details.html?id=123
    |_____base_url_____|_base path_|____page___| 
                                    

The page is also converted into normalized class name, therefore slashes are replaced with underscores, so in example above `page=user_details`.

Examples:

| BROWSER URL	| PAGE | NOTES |
|--------------|------|------|
| http://example.com/preferences.html | preferences | Extensions are ignored |
| http://example.com/?page=user/add |    user/add |	If Rewriting is off, page can be passed as an argument
| http://example.com/profile/change-password.do |	profile/changepassword |	Dashes cannot be used and are removed from page name |
| http://example.com/?abc=123 |	index|	Default page name is index |
| http://example.com/admin/logout	|logout	|base_path=admin, page=logout |

### Using pretty URLs

Most browsers have the capability to modify URLs. Agile Toolkit looks at the original URL so as long as you can direct your requests towards the `index.php` file, your application will work consistently on any web server.

#### Apache

    RewriteEngine On
    RewriteRule ^[^\.]*$            index.php   [L]

As mentioned before - pages may never contain dots. Therefore you can use that as a rule to direct all URLs without dots towards Agile Toolkit.

#### Nginx

Here is sample Nginx configuration

     TODO
     
## Securing Installation

Fundamentally Agile Toolkit comes with several concepts which should make it safe to have Agile Toolkit and your application to stay completely inside web-root.

 - All PHP files contain only a class definition
 - All Sensitive configuration stored declaratively inside config.php
 - Locations containing data which shouldn't be accessible secured with .htaccess files

A simple drop-inside-webroot approach is provided to simplify your first installation time. For a production installation you must consider moving your project files outside of your webroot. This can be done by following installation instructions at http://github.com/atk4/atk4 or --- link to tutorial.

Secure installation is completely transparent and does not require you to change your software code. The same project can be installed in different configurations through git, so you don't need to keep your application secure on your local laptop if you plan to install it securely on the server.

## Advanced Routes with PatternRouter

Agile Toolkit provides a very fundamental routing. There are two ways how to extend the controller. Firstly you can extend your application class to map the requested URL into an appropriate Page class. Normally layout_Content is doing that for you.

You can also take advantage of routing controller. Agile Toolkit comes with a built-in "PatternRouter" controller which allows you to link patterns to pages in a more flexible way.

    $this->add('PatternRouter')
        ->link('profile',array('id'=>'int'))
        ->link('article',array('article_code'));
        
<.. insert text from my email here about pattern router which i sent into google group ..>

## Handling the Request

So far we have looked at the way how URL is converted into `page` and `_GET arguments`. Let's talk about what the toolkit is doing with this information.

### 1. Look for method in Application class

Agile Toolkit typically uses camel case for naming methods. There are some exceptions where some specific methods needs to be distinguished. In this case application methods starting with `page_` can be executed from the web directly.

    function page_about($p){
        $p->add('LoremIpsum');
    }

The above method in your application will add support for "about" page. Method receives instance of a Page class, where you can add additional objects.

### 2. Look for custom page class

If method is not found in the Application class, the attempt is made to load a custom page class. Page classes are the only objects in Agile Toolkit which can be directly addressed from web, therefore all pages start with `page_` and reside in a separate folder `page/`. 

<.. i have this info on the web, we can reuse old docs ..>

### 3. Loading static pages

### 4. Executing $api->pageNotFound()

### Routing pages to add-ons.

Agile Toolkit makes it possible for some pages to be handled by add-on. For example if you are using OAuth add-on, it redirects 'auth' page (and it's sub-pages) towards the classes inside add-on's namespace. 

Here is the example scenario

 - page: "test/abc"
 - code in api: $this->api->routePages('auth','example/myaddon');
 - page class: example\myaddon\test_abc
 - page location: addons/example/myaddon/page/test/abc.php

