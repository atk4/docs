Application Class Features > API Classes
---

## Overview

Agile Toolkit uses the [Front Controller](TODO) pattern, where all requests are directed through a single entry point known as the API class.

The intial entry point for every Agile Toolkit app is the `index.php` file in the webroot directory. See the [Install Tutorial](TODO) for how to set this up in your webserver.

The main role of `index.php` is to bootstrap the correct API class for your app.

The API class has the job of initializing and coordinating the essential services required to respond to the request.

API classes run their own sessions and authentication entirely independently of each other, which is a powerful feature for building [multi-iterface applications](TODO).

## Choosing The Most Suitable Application Class

A neat feature of Agile Toolkit is the ability to use an application class tailored precisely to the job in hand.

The main APIs are:

* [ApiCLI](/TODO): a minimal frontend for command line requests

* [ApiWeb](/TODO): a minimal frontend for web applications, mainly used for integrating with other frameworks. 

* [ApiFrontend](/TODO): a comprehensive API for standalone web applications.

There are other more specialized APIs for installers and REST requests. 

Let's check out the main API classes in more detail.

### ApiCLI

`ApiCLI` offers the bare minimum features. It's mostly used to run command-line or CRON requests and offers the following features:

- PathFinder for locating files and lazy class loading
- Lazy configuration file access
- Lazy database connectivity
- Error handling & logging.

Here's Agile Toolkit's version of 'Hello World':

    // In index.php

    include 'atk4/loader.php';
    $api = new ApiCLI();
    echo "Hello World\n";

If you need database access, you should explicitly call `$api->dbConnect()`. 

With ApiCli your command-line apps can access your Models and Controllers though a compact interface without the complication of routing and Views.

### ApiWeb

`ApiWeb` adds the minimum features you need to interact with browsers through HTML:

- Logger (better error logging and reporting)
- PageManager (determining base URL and URL generation)
- HTML Headers (uses no-cache headers by default)
- A basic application template
- Pages and routing (for handling HTTP requests).

To make your web app more compliant with EC Cookie Law the session will only be initialized if you use memorize/recall to store session values. Some Controllers, such as Authentication, rely on memorize/recall and will cause the session to be initialized.

`ApiWeb` does not use Page classes, so if you want to display any Views you need to add them directly into the Application.

    // In index.php

    include 'atk4/loader.php';
    $api = new ApiWeb();
    $api->add('View_HelloWorld');
    $api->main();

### ApiFrontend

For a standalone web application you would normally use `ApiFrontend`.

This class adds a sophisticated [layout](/TODO) system to the functionality in `ApiWeb`. Layouts allow you to define regions inside your main template which are URL dependent. 

By default, the API will [map the request URL](TODO) to the related Page class. This serves up the response HTML to be included in the `&lt;?Content?&gt;` tag in the app's outer template. Both routing and layouts can be customised.

And ApiFrontend automatically initializes the `Logger` Controller &ndash; a nice, configurable error logger.

### ApiInstall

If your Agile Toolkit app requires installation you may want to create a simple interface which can help the user set up the software. 

Take a look at `ApiInstall` which might already offer what you need. It's a specialist API for building installers.

Using `ApiInstall` as an example, you might even create a completely new type of installer API which could, say, bind your Agile Toolkit app with the Wordpress or Joomla environment.

Encapsulating this functionality in an API class makes it more reusable, either for your in-house projects or for sharing with the community.

### Initializing The Application Class

Each API will initialize several sub-components which call Controllers. 

For a basic application you can initialise the Core application class directly in the `index.php` file and organize your response from there:

    // Run a CRON job directly
    // from index.php

    include'atk4/loader.php';
    $api = new ApiCLI();

    if($argv['1']){

        $class = $argv[1];

    }else{

        throw $api->exception('No job requested');
    }

    $api->dbConnect();
    $api->add($class);

For more complex apps it's better style to extend the correct API class and do your initilization in its `init()` method. This might include:

* Adding an authentication mechanism
* Customizing routing and layouts
* Setting up a G11n Controller
* Adding additional Controllers for application-wide access
* Placing a View on all pages of the app, such as a cookie notice or an image of the logged-in user.

Here's how it's done:

    // In /lib/Store.php

    class Store extends ApiFrontend{

        function init()
        {
            // Do your complex setup here

            $this->add('Controller_Translate');

            // ...
        }
    }

    // In /index.php

    include 'atk4/loader.php';
    $api = new Store('');
    $api->main();

<!--x Can we think of somewhere better to put this?? x-->

## Interrupting Execution Flow

It is possible to interrupt execution of the init() or render() phases. This might be necessary if you wish to avoid any more objects being added to a View. 

This technique is used by the `Auth` class. If a user is not logged in, `Auth` needs to display a login form while bypassing the initialization of any objects on your Page for security reasons. 

To terminate execution of `init()`, use:

    $this->exception('','StopInit');

 If you wish to stop rendering, you can use:

    $this->exception('','StopRender');

## Keeping Your APIs Clean

As we've seen, the role of the API class is initializing application-wide features such as authentication, and running Views that appear on every Page. 

While it might be tempting to add some business logic inside your API class, it's usually better to use a separate Controller.

That way, your code can be used in multiple applications without any duplication.

It's good style to keep your API classes clean and minimal.
