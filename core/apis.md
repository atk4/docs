# Core > API Classes
<!-- Reviewed by Romans: 1 April -->

## Overview

A neat feature of Agile Toolkit is the ability to use an application class tailored precisely to the job in hand.

The main APIs are:

* [ApiCLI](/TODO): a minimal frontend for command line requests
* [ApiWeb](/TODO): a minimal frontend for web applications, mainly used for integrating with other frameworks
* [ApiFrontend](/TODO): a comprehensive API for web applications, extending ApiWeb with routing, an integrated CSS styling system, and Page classes

There are other more specialized APIs for installers and REST requests. In process of developing your own application, you will have to create your own Application class extended from a standard Api class.

## The Purpose Of The API Classes

### Understanding Application Class

Agile Toolkit does not use any static elements or global variables. All objects you create are related to each-other. All objects also store reference to the Application class through `$this->api` property.

This leads to several interesting points:

- You can have more than one API running at the same time;
- APIs are independent, and will never share same ID, Cookie name or session name;
- You can create one Application from inside another one. The latter will function perfectly yet unaware of the former Application.

### Controllers versus Singletons

Agile Toolkit does not use singletons. Singletons do not provide any significant performance or persistence benefits. Instead, Agile Toolkit has the concept of "Controllers". A controller can be added into an object. If you attempt to add another controller of the same class into same object, you'll get reference to existing controller instead. This is a "soft" restriction: you can have more controllers of same type within object if you specify a different name for it.

#### Example
You may add "Logger" controller into the API to improve logging capabilities. Some other part of your application may also attempt to add "Logger" controller, however because it exists already it won't be added twice.

Because it's possible to have multiple APIs at the same time, this opens up new strategies for testing. For instance, a 'testing' API can initialize your 'real' API. Before executing a test, the 'test' API can destroy() some of the controllers in the 'real' API, and replace them with new ones. This implements a dependency-injection based testing framework, which is really close to the real-life environment of your application.

#### Realms

When the API class is initialized it expects only one argument: the "realm". A Realm must be unique to an application or even to an installation. The realm becomes the "name" of your application class, and it will also be used in all unique IDs (cookies, DOM id's, session variables).

Using unique realms results in your application being independent or sharing authentication and other things.

## Choosing most suitable application class

### ApiCLI

`ApiCLI` offers the bare minimum features. It's mostly suitable to run command-line or CRON requests. Application offers the following features:

- PathFinder for locating files
- Lazy configuration file access
- Lazy database connectivity
- Error handling & logging.
- PathFinder

Here's Agile Toolkit's version of 'Hello World':

    // In index.php

    include 'atk4/loader.php';
    $api = new ApiCLI();
    echo "Hello World\n";

If you are willing to access database, you should explicitly call `api->dbConnect()`. If you try to get some configuration options through `api->getConfig()`, application will load `config-default.php` and then `config.php` file.

### ApiWeb

`ApiWeb` adds the minimum features you need to interact with browsers through HTML. The following features are initialized automatically:

- Logger (better error logging and reporting)
- PageManager (determining base URL and URL generation)
- Headers (by default will use no-cache headers)
- Basic application template
- Pages and routing (for handling HTTP requests)

To make your web app more compliant with European Browser Cookie Law the session will only be initialized if you use memorize/recall. Some controller rely on memorize/recall (Authentication) and will cause session to be initialized.

ApiWeb does not use Page classes, so if you want to display any Views, you need to add them directly into Application.

    include 'atk4/loader.php';
    $api = new ApiWeb();
    $api->add('HelloWorld');
    $api->main();

### ApiFrontend

Finally, `ApiFrontend` adds sophisticated [layouts](/TODO) system into your application. Layouts allow you to define a certain regions inside your main template, which are URL dependent. By default, &lt;?Content?&gt; area will be substituted by an adequate page class.

Mapping URL into Page class is the default routing mechanism of Agile Toolkit.

#### Execution of he Application Class

An Application class in Agile Toolkit splits the whole execution of the application in 2 parts. In the first part, the API initializes all the objects, coupling them with proper models. The second part is the execution: the API will determine which part of the page needs rendering, will cause objects to run their queries and produce HTML, then send output to the browser.

Agile Toolkit objects never `echo` anything directly. Instead, they insert their output into their parent's template. The API sends the combined output to the user's browser through a single `echo` command.

#### Initialization

Depending on which API you use, it will initialize several sub-components which call Controllers. When you extend an API class, you should re-define the init() function and add additional initialization in there - things like adding authentication mechanism, extending routing rules, adding controllers or placing View on all the pages (Such as a image of currently logged-in user).

ApiFrontend will automatically initialize Logger() which makes error logging more bearable.

#### Layouts

A Layout is a region in the shared template (shared.html), which is parsed by the API class. If the region is defined, then a corresponding layout function is called.

The first and most significant layout element is "Content". Content will determine which page is requested and proceed with all the logic of page initialization. However if "Content" is not present in the API template (`shared.html`), the page will not be initialized.

You can add additional layout elements. For example, calling addLayout('Menu') inside api->init() which will instruct API to call layout_Menu() method if tag &lt;?Menu?&gt; is found when parsing the template.

This approach is designed to have multiple layout components initialized based on the use of global template. This way your application can easily produce output for multiple form-factor devices. When opened on a mobile device, your application can load a different application template (such as `shared-mobile.html`) which would omit some tags and bypass initialization of certain regions.

You can specify which template file is used by specifying it from inside the defaultTemplate() function of your API.

    function defaultTemplate(){
        if($_GET['mobile']){
            return array('shared-mobile');
        }else{
            return array('shared');
        }
    }

#### Using user-authentication

Agile Toolkit comes with an "Auth" controller. This controller allows you to limit access to your application to logged-in users only. Here is a most basic use of the Auth controller:

    function init(){
        parent::init();
        $this->add('Auth')->allow('john','secret')->check();
    }        

In most modern applications you would want to use users from a database, so you should specify a model instead:

    function init(){
        parent::init();
        $auth=$this->add('Auth');
        $auth->setModel('User');
        $auth->usePasswordEncryption();
        $auth->check();
    }
        
When `$auth->check()` is executed, it will interrupt your regular application flow to display a login form for non-authenticated users.

#### Form Submission

Application supports number of hooks such as `post-init` or `pre-render`. By adding your handlers there, you can execute certain things between the two major execution steps.

There is one more step in between - form submission. It is used as a call-back into a standard `Form` class. Forms want to parse POST data after they have been fully initialized but before they start rendering.

Forms use unique IDs to determine if they have been submitted, so even if you have multiple forms on the page, they will be able to determine which one have been submitted without your help.

#### Rendering

Agile Toolkit provides support for partial rendering of views based on 3 criteria: cut_page - outputs only page's content (used by dialogs and frames), cut_object - outputs content of a certain object (used when object is reloaded) and cut_region - outputs content of a certain region in template. (cut_region is not safe and should be avoided). 

#### Interrupting initialization phase

It is possible to interrupt execution of the init() or render() phases. This might be necessary if you wish to avoid any more objects being added to some view. This technique is used by the Auth class. If user is not logged in, Auth class wants to display a login-form but it wants to bypass initialization of any objects on your page for security reasons. 

To terminate execution of init, use:

    $this->exception('','StopInit');

This exception is automatically intercepted and ignored by the Application class. If you wish to stop rendering, you can use:

    $this->exception('','StopRender');


## Structuring A Multi-API Application

You should view your Application class as a "glue". While it might be tempting to add some business logic inside your application class, try to put it into a separate controller.

This will help you to create multiple application classes without duplicating any of the main logic.

#### Multiple Interfaces

Most web software use multiple web interfaces. User Frontend and Admin are separate Web interfaces, both of which take advantage of the page concept. While backend functionality is shared between both - Models, Controllers, only Administrators may access the Admin interface so the "Auth" class would be configured differently.

#### Command-line utilities

If you want to execute your application-specific code from command-line you wouldn't use any Views or Pages. Created a ApiCLI based application and then accessing your models and controllers from it is an ideal way to create compact interface to your application logic.

#### Multi-lingual or satellite sites

Sometimes you will want your software to run in multiple regions mostly unchanged, however you need to have the flexibility to add regional or site-specific changes. In this case you will want to create an intermediate API (parent), which is then extended for each regional site.

Site1 and Site2 API classes can change theme, skin or add additional templates/translations for localization, but would rely on the same common Application class for all the base functionality.

#### Specific-purpose applicaiton

If your Agile Toolkit-based software requires installation, you may want to create a simple interface which can help the user set up the software. 

Take a look at `ApiInstall` which might already offer you what you need. It is a specific implementation of Application for building installers.

You might want to create a completely new type of Application which could bind your Agile Toolkit environment with Wordpress or Joomla environment.

This way you can share the generic installer class with the community, or with other projects while retaining the flexibility of customization.
