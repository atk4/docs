# Anatomy Of An Application

Before diving into PHP code, I would like you to look at the file structure of your application and understand how to locate files you need. 

Agile Toolkit also takes special care to distribute files into separate folders depending on how are they used or who wrote them.

## The Project Folder

When you downloaded Agile Toolkit archive and extracted it for the first time, you undoubtedly found an "agiletoolkit" folder which were instructed to place somewhere in your Web Server Documents folder. This is your Project folder.

While in Sandbox, your project folder must the WRITABLE by PHP. For Mac and Windows MAMP/WAMP environments it is already writable, but if you are using Linux workstation, you might need to change "User" setting for PHP to have access to your files. (Files are not writable on production server)

    admin/
    frontend/
    shared/
    
    agiletoolkit-sandbox.phar

    db/
    vendor/
    
    
    composer.json
    composer.lock
    composer.phar
    
    config-sandbox.php
    config-default.php
    dependencies
    
    index.php
    
### Interfaces (admin and frontend)

Interface is a sub-set of your project aimed at a specific type of use. By default your project contains "admin" for restricted administrative and "frontend" interface for public access. You may create more interfaces for testing, commant-line use, cron jobs, API or other web interfaces. 

Web interfaces must contain "public" folder, which will be used as a web-root on your production server. A document root of www.myproject.com would point to `/path/to/project/frontend/public`.

### shared

This folder contains resources which are shared between interfaces. Typically your Models will shared, however you may want to place your own "addons" and "templates" in shared folder.

### agiletoolkit-sandbox.phar

Agile Toolkit Sandbox in a development environment for Agile Toolkit. This archive contains the necessary code to operate Agile Toolkit on your local computer. This file contains communication tools between your project and agiletoolkit.org and necessary code to install add-ons, help you configure database, debug and find problems in your code. 

This archive also contains Agile Toolkit installer which you will see if you direct your browser to through web server to your project directory.

AgileToolkit Sandbox is NOT installed on the server when you publish. If you delete this file.

### db

Folder required by agiletoolkit-sandbox.phar to store local application statistics. This folder contains Sqlite database which should never be uploaded to your production environment. It stores debug info such as request log, extensive error tracing info and so on.

### vendor, composer.*

`vendor` folder contains 3rd party add-on code and is managed by composer.phar. You shouldn't change composer.json or composer.lock as those files are updated automatically by Agile Toolkit installer.

### configuration files

Agile Toolkit recognizes the following configuration files:

 - config-sandbox.php will contain automated settings for your Sandbox environment. This file is updated automatically by Agile Toolkit Sandbox.
 - config-default.php contains default settings for your application and will synchronized.
 - config.php can be created to to locally override default settings.
 - dependencies - used internally by Agile Toolkit Sandbox
  

## The Class Hierarchy

To implement the [Composability](/TODO) and [Extensibility](/TODO) that make Agile Toolkit so productive we need to provide every object with a number of common features.

So at the top of the class hierarchy is `AbstractObject`, which primarily handles the creation, naming and initialization of objects.

As we've seen, there are three classes that extend directly from `AbstractObject`:

* **AbstractModel**: which adds features to help manage data entities
* **AbstractView**: which adds features for generating user interface output with nested templates
* **AbstractController**: which is the root class for all Controllers. 

![ATK Object Structure](dia-abstract-object.png)

All other classes descend from one of these three abstract classes.

## The Runtime Object Tree

In a related departure from conventional design, objects in Agile Toolkit are always instantiated inside a parent object using the `AbstractObject add()` method rather than the PHP `new` statement. Again, this is to assist with our Composability design goal. 

So an Agile Toolkit application is a runtime tree of objects nested inside other objects. 

Because of the focus on UI development, the application object at the top of the tree is a View, responsible for recursively rendering the output from all the child Views.

The Page View contains the top-level UI objects such as menus and tabs, and they contain more detailed Views such as forms, fields and buttons down as many levels of nesting as required. And within data-aware views like grids you'll find the necessary Models and Controllers.

When developing the UI, your primary task is to add the Views you need inside a parent View and associate them with the appropriate Model and any required Controllers. 

![ATK Application Structure](dia-application.png)

## The Application Object

As we've said, the topmost object in the Runtime Object Tree is your Application object `$api`, which is extended from an API class. Most of the Toolkit Core features are provided through this object. 

There are a number of API classes available in the Core, and you can extend them yourself to add any specific features you commonly require. The main Core APIs are:

* [ApiCLI](/TODO): a minimal frontend for command line requests
* [ApiWeb](/TODO): a minimal frontend for web applications, mainly used for integrating with other frameworks
* [ApiFrontend](/TODO): a comprehensive API for web applications, extending ApiWeb with routing, an integrated CSS styling system, and Page classes

There are other more specialized APIs for installers and REST requests.

API classes descend from `AbstractView`, because they are generally used to create output.

## The Execution Flow

Now you understand the Runtime Object Tree and the `$app` object we can walk though the basic anatomy of an HTTP request.

![Agile Toolkit Execution Flow](dia-execution-flow.png)

1. First, your webserver is configured to run the `index.php` file in your webroot for all page requests. This simply bootstraps your application with 3 lines of code:

    <pre>
    include 'atk4/loader.php';

    // Set up the application object
    $api=new Frontend('sample_project');

    // Run the application
    $api->main();
    </pre>
    
1. `Frontend.php` is the `$app` class, extended from the `ApiFrontend` API.

1. In `Frontend.php` you configure your class loading, initialize access security and add Controllers for application-wide functionality such as database access and logging.

1. The `$app` object will route the request to the correct Page class in your `/page` directory, throwing a 404 Page Not Found error if no matching page is found.

1. The Page will orchestrate the necessary top-level Views, and these will load any additional Views, Models and Controllers they require, recursively down the Runtime Object Tree.

1. Once all objects are initialized, the `$app` will recursively render the nested tree. Views use the SMlite template engine to generate their HTML output, and Models use the DSQL (Dynamic SQL) Query Builder to generate runtime SQL. 

1. Finally, the `$app` object will echo out the HTTP response as HTML, XML or whatever is required.

## The Application Core

The Agile Toolkit Core sits in the `/atk4` directory and handles the plumbing of your application. The key features are:

* [Configuration](../application/configuration.md): configuring the application and Controllers
* [Class Loading](../application/class-loading.md): automatic lazy loading of classes as they are required
* [Request Routing](../application/routing.md): loading the correct Page class to handle the request
* [Asset Management](/TODO): serving CSS, JavaScript, image and other media files
* [Event Hooks](../applicaiton/hooks.md): a callback mechanism for running code at specific points in the execution process
* [Authentication & Authorization](/TODO): controlling access to the application and its features
* [Error Handling](../application/exception-handling.md): help with handling and logging various types of error
* [Testing](../application/testing.md): a simple but rather useful built-in unit testing framework.
