# DAY 1: STARTING THE PROJECT

## Pre-Introduction
---
Before I will go into details, I have to say a huge thanks to the Fabien Potencier and Jonathan Wage for providing the original tutorial for Symfony and its port to SF2 [http://sftuts.com/doc/jobeet/en/index.html](http://sftuts.com/doc/jobeet/en/index.html "http://sftuts.com/doc/jobeet/en/index.html") as well as [http://symfony.com/legacy/doc/jobeet/1_4/en/01?orm=Doctrine](http://symfony.com/legacy/doc/jobeet/1_4/en/01?orm=Doctrine "http://symfony.com/legacy/doc/jobeet/1_4/en/01?orm=Doctrine")

I have attempted to follow up through all the ideas and reasoning in creation of the application as well as best practices. However, Instead of using Symfony I am using Agile Toolkit.

Contents of this book is distributed under Creative Commonts Attribution - ShareAlike license

## GitHub branches for Each Day
----

The source code for this project is hosted on GitHub. In fact it has branch for every day. [https://github.com/romaninsh/jobeet](https://github.com/romaninsh/jobeet "https://github.com/romaninsh/jobeet"). If you wonder how much code is changed every day, run:

    git diff day3..day4
    
## Introduction
----

The Agile Toolkit has been developed for over 10 years as a commercial product and only recently have been released under Open-Source license. Agile Toolkit is one of the simplest frameworks to use and comes with a great User Interface.

This book describes the creation of a web application with the Agile Toolkit, step-by-step from the specifications to the implementation. It is targeted at beginners who want to learn Agile Toolkit, understand how it works, and also learn about the best web development practices.

The application to be designed could have been yet another blog engine. But we want to use Agile Toolkit on a useful project. The goal is to demonstrate that Agile Toolkit can be used to develop professional applications with style and little effort.

We will keep the content of the project secret for another day as we already have much for now. However, let’s give it a name: Jobeet.

Each day of this book is meant to last for 20 minutes to a hour, and will be the occasion to learn Agile Toolkit by coding a real website, from start to finish. Every day, new features will be added to the application, and we’ll take advantage of this development to introduce you to new Agile Toolkit functionalities as well as good practices in Agile Toolkit web development.

## Agile Toolkit is a different framework
----
Validation, error handling, security, and tests are first-class citizens in many frameworks. However in most cases, frameworks only supply the tools, but developers must connect the dots. Agile Toolkit does many things on it's own. A very strong focus on "convention" is something many will love and many will hate, but that is what makes Agile Toolkit.

All the code you will read in this book is code you could use for a real project. We encourage you to copy and paste snippets of code or steal whole chunks.

## What for Today?
----
We won’t write PHP code. But even without writing a single line of code, you will start understanding the benefits of using Agile Toolkit, just by bootstrapping a new project.

The objective of this day is to setup the development environment and display a page of the application in a web browser. This includes installation of Agile Toolkit, creation of an application, and web server configuration.

As this book will mostly focus on the Agile Toolkit framework, we will assume that you already have a some basic knowledge of PHP 5 and Object Oriented programming.

###Prerequisites
Before installing Agile Toolkit, you need to check that your computer has everything installed and configured correctly. Take the time to conscientiously read this day and follow all the steps required to check your configuration, as it may save your day further down the road.

### Third-Party Software
First of all, you need to check that your computer has a friendly working environment for web development. At a minimum, you need a web server (Apache, for instance), a MySQL, and PHP 5.2 or later.

You can run Agile Toolkit software on Windows, Linux or Mac. It does not make use of command line, does not require any files or directories to be writable and will work in most of the environments without changes

## Downloading and Installing Agile Toolkit
----
Ready? Now let’s start by downloading Agile Toolkit. For this guide you will need 4.2+ version of Agile Toolkit. There are 2 main components in Agile Toolkit - core and addons. They are distributed individually, but are also available as a example-project download from [Agile Toolkit Download Page](http://agiletoolkit.org/download "Agile Toolkit Download Page")

If you plan to use Git, then follow instructions on the download page which will help you get your own repository with the bootstrapped code.

Agile Toolkit can work with virtually almost no code and in order to show few things, the example project already includes many things you can play with.

Download and unpack will create the atk4-example directory. Let's rename it into 'jobeet' for the time being.

    www/ <-- your webroot
    jobeet/ <-- your new directory
    atk4 <-- Agile Toolkit Core
    atk4-addons <-- Agile Toolkit Addons
    lib/ <-- Class Library
    page/ <-- Page Classes
    doc/ <-- Your project documentation and schemas
    templates/ <-- Your project custom UI templates
    index.php <-- Front-Controller or Catch-all for all requests
    config-default.php <-- Your app's default configuration
    config-distrib.php <-- Your app's per-installation sensetive configuration
Agile Toolkit runs perfectly fine regardless of how it is installed. Point your browser at http://localhost/jobeet and you should see page with text:

*Hello World from your own copy of Agile Toolkit*

Congratulations, Agile Toolkit is installed!

### Realm
An essential part of your project is realm. It can be changed in index.php file and is a first argument in the line:

    $api=new Frontend('sample_project');

## Security
----
### Directory Structure Rights
Agile Toolkit does not require to set any rights without reason. There are however cases when you want logging or file uploads to work. We recommend not to make any writable directories unless it's absolutely necessary. Agile Toolkit is a lightweight framework and makes no use of file-cache.

### Files
Agile Toolkit uses only .php file extenions on purpose (even for configuration files). This is do avoid any need for you to disable any files in web directory (or possibly forgetting about it). It is safe to leave Agile Toolkit in the space accessible from the web. All the PHP files (except for atk4/loader.php and atk4/lib/static.php) contain only classes and will do nothing if requested directly. Be sure not to call files like config.php.distrib and turn off backups in your text editor.

[Hide .svn and .git directories in your webserver config!](#)

    It is however advisable to disable access to .svn and .git directories. It's surprising how many guides manage to ommit this and as a result it may potentially give access to your sensetive information.

##Webroot access
Many guides suggest to place files outside of the webroot directory for security reason. If you are using Agile Toolkit, there is no need to worry about that. If you do want to follow this rule, then you can move "atk4" folder elsewhere along with addons. Create alias /atk4/templates if you plan on using default UI.

## Your files VS library files
----
It is important that you never change files inside atk4/ or atk4-addons directories. You might overwrite them yourself later on when you are doing upgrade. However if you want to substitute any of those files in your project, you can do it by creating them outside atk4 directory.

As an example, take a look at the logo in the left-top corner. Let's replace it with something different. Look into the source of the page. It uses the following URL: /jobeet/atk4/templates/jui/images/atk-logo.png by default. Copy your own logo into jobeet/templates/jui/images directory (create it if necessary) and name it atk-logo.png. Agile Toolkit will automatically notice this file and will use instead of default logo. The same rule applies to ANY file. Files outide atk4 folder will be included first.

p.s.: default logo uses alpha channel so if you try look at it in your browser it seems to be invisible

## Configuration
----
By default Agile Toolkit will output errors on the screen. Click on "database test" and if your MySQL connection is not configured yet, you most likely will see an exception. Having exceptions on a production environment is bad and there is a way to control your environment.

Copy file config-distrib.php into config.php. Never add config.php to Git or Subversion. It will contain only things specific to your installation. Let's look closer into the file and set some settings there:

    $config['atk']['base_path']='./atk4/';
    $config['dsn']='mysql://root:root@localhost/project';
Additionally we would want to do extensive use of database in Jobeet therefore uncomment the following line in lib/Frontend.php

    //$this->dbConnect();
Change those to conform with your installation and let's add a few more lines to redefine settings from config-default.php:

    $config['atk']['base_path']='/jobeet/atk4/';
    $config['dsn']='mysql://jobeet:secret@localhost/jobeet';

### Beautiful URLs
Since it's quite common practice, if you have mod_rewrite enabled, try to set it right away. If this does not work for you, you can skip this section, it's not essential for the functionality of your application.

To use beautiful URLs you need to instruct Agile Toolkit to use different URL generation rules by adding 2 lines into config.php file:

    $config['url_postfix']='.html';
    $config['url_prefix']='';
Next you will need to create a .htaccess file:

    RewriteEngine on
    RewriteRule ^(index.php.*)$      $1          [L]
    RewriteRule ^[^\.]*$            index.php    [L]
    RewriteRule .html$              index.php    [L]
URLs generated by the menu now should look pretty

## Final Thoughts
----
Well, time is over! Even if we have not yet started talking about Jobeet, we have setup a solid development environment, we have talked about web development best practices, and we are ready to start coding. Tomorrow, we will reveal what the application will do and talk about the requirements we need to implement for Jobeet.