
# Advanced Topics: TO BE REMOVED
# Advanced Topics: TO BE REMOVED below

     
## Securing Installation

Fundamentally Agile Toolkit comes with several concepts which should make it safe to have Agile Toolkit and your application to stay completely inside web-root.

 - All PHP files contain only a class definition
 - All Sensitive configuration stored declaratively inside config.php
 - Locations containing data which shouldn't be accessible secured with .htaccess files

A simple drop-inside-webroot approach is provided to simplify your first installation time. For a production installation you must consider moving your project files outside of your webroot. This can be done by following installation instructions at http://github.com/atk4/atk4 or --- link to tutorial.

Secure installation is completely transparent and does not require you to change your software code. The same project can be installed in different configurations through git, so you don't need to keep your application secure on your local laptop if you plan to install it securely on the server.


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

